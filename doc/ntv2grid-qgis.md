Door: Bas - originele email: https://lists.osgeo.org/pipermail/dutch/2016-October/001508.html

Een van de vragen rondom de NTv2 grid shift files van Kadaster was hoe
deze te gebruiken in QGIS, het installeren van de proj-rdnap package op
Debian/Ubuntu is daarvoor niet genoeg (dit maakt ze slechts beschikbaar
voor PROJ.4).

QGIS gebruikt een SQLite database voor zijn diens projecties (srs.db) en
synchroniseert deze bij de installatie met de definities gebruikt door GDAL.

Hiervoor wordt onder andere de epsg.wkt file van GDAL gebruikt en op
Debian/Ubuntu en Fedora zijn de twee include files daarin
(esri_extra.wkt & cubewerx_extra.wkt) niet beschikbaar omdat er geen
licentie voor bekend is (en zodoende onder strikte copyright valt). In
cubewerx_extra.wkt is o.a. EPSG:900913 gedefinieerd waardoor de Google
Mercator projectie komt te vervallen.

Gelukkig bied QGIS de mogelijkheid om zelf een CRS te definieren (via
Settings -> Custom CRS...), dit is de aangewezen methode om ontbrekende
projecties toe te voegen. [3] Deze projecties worden in het user profile
opgeslagen (in qgis.db) worden zodoende ook niet overschreven bij een
upgrade van de software, wat wel gebeurd als
/usr/share/qgis/resources/srs.db wordt aangepast.

Zowel de naam als de parameters voor de Custom CRS kunnen uit de
configuratie van de proj-rdnap package overgenomen worden
(/usr/share/proj/rdnap) [4]:

```
 Name:       RDNAP with NTv2 and VDatum
 Parameters: +proj=sterea +lat_0=52.15616055555555
+lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000
+ellps=bessel +nadgrids=rdtrans2008.gsb +geoidgrids=naptrans2008.gtx
+units=m +no_defs <>
```
```
 Name:       RD with NTv2 only
 Parameters: +proj=sterea +lat_0=52.15616055555555
+lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000
+ellps=bessel +nadgrids=rdtrans2008.gsb +units=m +no_defs <>
```

Om de Custom CRS te testen kan bijvoorbeeld Amersfoort gebruikt worden
welke ook in de tests coordinaten is opgenomen [5]:
```
 Geographic / WGS84    Destination CRS

 North: 52.155172897   462,999.9999
 East:  5.387203657    155,000,0000
```
Volgens de test sheet had dit 463000.0000 / 155000.0000 moeten zijn, de
minimale afwijking bij gebruik van de NTv2 grid shift files is bekend en
valt binnen de marge van 0.001 meter.

* [3]
http://docs.qgis.org/2.14/en/docs/user_manual/working_with_projections/working_with_projections.html#sec-custom-projections
* [4]
https://anonscm.debian.org/cgit/pkg-grass/proj-rdnap.git/tree/debian/rdnap
* [5]
https://anonscm.debian.org/cgit/pkg-grass/proj-rdnap.git/plain/Use%20of%20RDTRANS2008%20and%20NAPTRANS2008.pdf
