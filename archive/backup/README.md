# notes.busilogic.com

This site is mostly system admin notes I keep for myself in org mode format.

They are converted to HTML using org mode > ver 8

Run on the org file itself:
```
M-x org-html-export-to-html
```

The index file is a site-map reference to the org files

## Hosting
* The html files are hosted on my freebsd server by nginx in a webserver jail
* To deploy, go to /var/www/notes.busilogic.com and do a git pull

