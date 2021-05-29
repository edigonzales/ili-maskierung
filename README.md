# ili-maskierung

## Datenbank
Die etablierte Lösung mit den GRETL-development-db-Skripten funktioniert mit Apple Silicon nicht. Auf die Schnelle ein Dockerimage hingefrickelt indem ich das offizielle postgis-Repo gecloned habe:

```
export VERSION=13-3.1
export VARIANT=default
```

```
docker run  --name postgis -p 54322:5432 -e POSTGRES_DB=pub -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres postgis/postgis:13-3.1

CREATE EXTENSION "uuid-ossp";
```

## UML-INTERLIS-Editor
Mein Fork des Forks hat anscheinend mit der Vererbung Mühe. Es kommt immer die Fehlermeldung, dass die Klassennamen nicht identisch sind (bei EXTENDED). Originalversion 3.7.6 funktioniert tadellos.

## ili2pg

### smart1 (default)
```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_smart1 --models SO_ALW_Bienenstandorte_20210526 \
--defaultSrsCode 2056 --createGeomIdx --createFk --createFkIdx --createUnique --createEnumTabs --beautifyEnumDispName --createMetaInfo --createNumChecks --nameByTopic --strokeArcs \
--createBasketCol \
--modeldir ".;http://models.geo.admin.ch" \
--schemaimport
```

```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_smart1 --models SO_ALW_Bienenstandorte_20210526 \
--topics SO_ALW_Bienenstandorte_20210526.Bienenstandorte_protected \
--modeldir ".;http://models.geo.admin.ch" \
--disableValidation \
--export protected.xtf
```

Falls ein - gemäss Modell - zwingendes Attribut fehlt, wird der Fehler gemeldet.

```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_smart1 --models SO_ALW_Bienenstandorte_20210526 \
--topics SO_ALW_Bienenstandorte_20210526.Bienenstandorte \
--modeldir ".;http://models.geo.admin.ch" \
--disableValidation \
--export public.xtf
```

### Zwei Modelle 
```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_2m --models SO_ALW_Bienenstandorte_protected_20210529 \
--defaultSrsCode 2056 --createGeomIdx --createFk --createFkIdx --createUnique --createEnumTabs --beautifyEnumDispName --createMetaInfo --createNumChecks --nameByTopic --strokeArcs \
--createBasketCol \
--modeldir ".;http://models.geo.admin.ch" \
--schemaimport
```

```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_2m --models SO_ALW_Bienenstandorte_protected_20210529 \
--modeldir ".;http://models.geo.admin.ch" \
--disableValidation \
--export protected_2m.xtf
```

```
java -jar /Users/stefan/apps/ili2pg-4.5.0/ili2pg-4.5.0.jar \
--dbhost localhost --dbport 54322 --dbdatabase pub --dbusr postgres --dbpwd postgres \
--dbschema alw_bienenstandorte_2m --models SO_ALW_Bienenstandorte_protected_20210529 \
--exportModels SO_ALW_Bienenstandorte_20210529 \
--modeldir ".;http://models.geo.admin.ch" \
--disableValidation \
--export public_2m.xtf
```

Beim Schema anlegen, muss in t_ili2db_basket ein "protected"-Topic / -Basket angelegt werden.
```
WITH topics AS 
(
    SELECT DISTINCT 
        split_part(iliname, '.', 1) || '.' || split_part(iliname, '.', 2) AS topicname
    FROM 
        alw_bienenstandorte_2m.t_ili2db_classname
    WHERE 
        iliname ILIKE '%protected%'
)   
INSERT INTO 
    alw_bienenstandorte_2m.t_ili2db_basket
    (
        t_id,
        topic,
        attachmentkey
    )
SELECT 
    --9223372036854775807
    nextval('alw_bienenstandorte_2m.t_ili2db_seq'),
    topics.topicname,
    'foo'
FROM 
    topics
```


## Beobachtungen / Fragen
- t_type ist immer "protected"? 
- Mandatory Attribute sind in der DB nicht mehr not null.
- Man benötigt immer "createBasketCol"?
- Beim Datenumbau müssen zwei zusätzliche Attribute mit statischen Werten abgefüllt werden (t_type und t_basket).

