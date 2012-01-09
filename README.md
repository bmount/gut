This is the as-deployed version of the gut shapefile converter at

[http://gut.sfgeo.org](http://gut.sfgeo.org)

## Setup

In the sfgeo.org implementation, `_shp2json` and `_shp2kml` run on 8088 
and 8089, respectively, and sit behind a CouchDB http proxy. To set up
a proxy node process as a CouchDB daemon, add the following lines to your
couchdb configuration (the file `local.ini` under `prefix/etc`):

```
[httpd_global_handlers]
;_google = {couch_httpd_proxy, handle_proxy_req, <<"http://www.google.com">>}
_shp2json = {couch_httpd_proxy, handle_proxy_req, <<"http://localhost:8088">>}
_shp2kml = {couch_httpd_proxy, handle_proxy_req, <<"http://localhost:8089">>}
```

```
[os_daemons]
; For any commands listed here, CouchDB will attempt to ensure that
; the process remains alive while CouchDB runs as well as shut them
; down when CouchDB exits.
;foo = /path/to/command -with args
shp2json = /usr/local/bin/node /home/you/gutserver/gut/shapefile-to-json/server.js
shp2kml = /usr/local/bin/node /home/you/gutserver/gut/shapefile-to-kml/server.js
```

The only modifications to stock `shp2json` are to add a kml flag to `ogr2ogr`,
see here at [https://github.com/bvmou/gut/blob/gut.sfgeo.org/shp2kml/index.js#L51](Line 51)

The only modification to stock gut server is to add a `content-disposition` header
to typically large files, so that they will be downloaded in a familiar way to users
via the web interface (curl etc should ignore them.)
