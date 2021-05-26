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


## Beobachtungen / Fragen
- t_type ist immer "protected"? 
- Mandatory Attribute sind in der DB nicht mehr not null.
- Man benötigt immer "createBasketCol"?