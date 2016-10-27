Door: Bas, originele email: https://lists.osgeo.org/pipermail/dutch/2016-October/001505.html

Bij de seminar werd geopperd om vanuit OSGeo.nl een pagina met de juiste
waardes voor RD in proj bij te houden, zodat dit eenvoudiger te vinden
is dan te moeten graven naar de discussies op deze lijst.

Resultaat van de discussie rondom de NTv2 grid shift files was dat geen
aanpassing meer nodig is voor recente versies van PROJ.4, PostGIS en
GeoTIFF om de juiste towgs84 waardes, zoals gepubliceerd door Kadaster,
te gebruiken.

Dankzij de discussie met Lennard in 2014-2016 op deze lijst [0] zijn er
bugs gevonden in GDAL [1] en PROJ.4 [2] welke resulteerde in incorrecte
waardes voor EPSG:28992 en fouten bij gebruik van de NTv2 grid shift files.

* [0] https://lists.osgeo.org/pipermail/dutch/2014-October/000980.html
* [1] https://lists.osgeo.org/pipermail/dutch/2015-February/001105.html
* [2] https://lists.osgeo.org/pipermail/dutch/2016-February/001377.html

De correcte towgs84 waardes vanaf 2009 zoals gepubliceerd door Kadaster
zijn:

```
 +towgs84=565.4171,50.3319,465.5524,-0.398957,0.343988,-1.877402,4.0725
```

Ik heb de towsg84 waardes in PostGIS, PROJ.4 & GeoTIFF gecontroleerd
voor de recente en oudere versies beschikbaar in Debian & Ubuntu.


PostGIS
-------

Deze waardes worden in ieder geval gebruikt door PostGIS 2.3.0 (in
Debian testing/unstable) in diens spatial_ref_sys.sql:
```
 TOWGS84[565.4171,50.3319,465.5524,-0.398957,0.343988,-1.87740,4.0725]
```
Ook PostGIS 2.2.2 (in Ubuntu yakkety) gebruikt deze waardes.

PostGIS 2.2.1 (in Ubuntu xenial) gebruikt deze waardes ook, maar heeft
een hogere precisie:
```
 TOWGS84[565.4171,50.3319,465.5524,-0.398957388243134,0.343987817378283,-1.87740163998045,4.0725]
```
PostGIS 2.1.8 (in Ubuntu wily) hebben een aantal waardes een lagere
precisie:
```
 TOWGS84[565.417,50.3319,465.552,-0.398957,0.343988,-1.8774,4.0725]
```
Zo ook in PostGIS 2.1.4 (in Debian jessie), en PostGIS 2.1.2 (in Ubuntu
trusty).


PROJ.4
------

PROJ.4 4.9.3 (in Debian testing/unstable) gebruikt dezelfde waardes als
PostGIS 2.3.0 in diens nad/epsg:

```
 +towgs84=565.4171,50.3319,465.5524,-0.398957,0.343988,-1.87740,4.0725
```

PROJ.4 4.9.2 (in Ubuntu xenial & yakkety) gebruikt dezelfde waardes als
PostGIS 2.2.2 met de hogere precisie:

```
 +towgs84=565.4171,50.3319,465.5524,-0.398957388243134,0.343987817378283,-1.87740163998045,4.0725
```

PROJ 4.9.1 (in Ubuntu wily) gebruikt deze waardes ook.

PROJ 4.8.0 (in Debian jessie & Ubuntu trusty) gebruikt dezelfde waardes
als PostGIS 2.1.x met de lagere precisie:

```
 +towgs84=565.417,50.3319,465.552,-0.398957,0.343988,-1.8774,4.0725
```

GeoTIFF
-------

In GeoTIFF zitten de towgs84 waardes niet zoals in PostGIS & PROJ.4, het
bevat wel de EPSG data die als bron wordt gebruikt voor de towgs84
waardes in PostGIS & PROJ.4.

GeoTIFF 1.4.2 gebruikt de EPSG v8.9 data, en daarin zijn de
transformaties tussen RD/NAP en ETRS89 met behulp van het NTv2 en VDatum
grids (EPSG:7000 & EPSG:7001) [3] in opgenomen.

[3] https://lists.osgeo.org/pipermail/dutch/2015-February/001097.html

csv/coordinate_operation.csv:

```
 7000,Amersfoort to ETRS89 (7),transformation,4289,4258,RDNAP-Nld
2008,7,1275,"Approximation of horizontal component of official 3D
RDNAPTRANS(TM) transformation, which since 1st October 2000 has defined
Amersfoort geodetic datum.",0.001,9615,,,Consistent to within 1mm with
official RNAPTRANS(TM)2008 at ground level onshore and at MSL offshore.
The horizontal deviation using this NTv2 grid is approximately 1mm per
50m height difference from ground level or MSL.,"Kadaster and
Rijkswaterstaat CIV, working together under the name
RDNAP.",OGP,2015/02/12,2015.006,1,0
 7001,ETRS89 to NAP height (1),transformation,4937,5709,RDNAP-Nld
2008,1,1275,"Derivation of gravity-related heights from GPS
observations. When used in conjunction with transformation Amersfoort to
ETRS89 (7) (code 7000), this transformation is
reversible.",0.01,9665,,,Alternative to vertical component of official
3D RDNAPTRANS(TM)2008. The naptrans2008 correction grid incorporates the
NLGEO2004 geoid model plus a fixed offset.,"Kadaster and Rijkswaterstaat
CIV, working together under the name RDNAP.",OGP,2015/02/12,2015.006,1,0
```

csv/coordinate_operation_parameter_value.csv:

```
 7000,9615,8656,,rdtrans2008.gsb,
 7001,9665,8666,,naptrans2008.gtx,
```

GeoTIFF 1.4.1 (in Ubuntu wily, xenial & yakkety) gebruikt de EPSG v8.5
data, en daarin zitten geen relevante wijzigingen.

GeoTIFF 1.4.0 (in Debian jessie & Ubuntu trusty) gebruikt de EPSG v7.9
data, en ook daarin zitten geen relevant wijzigen.

Disclaimer met betrekking tot de wijzigingen in GeoTIFF, de complexiteit
van de CSV bestanden maakt het analyseren niet eenvoudig. Ik kan
daardoor relevante wijzigingen over het hoofd hebben gezien.
