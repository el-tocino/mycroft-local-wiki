# Mycroft Local Wikipedia Skill

Alter your local copy of the wikipedia python package to update the URL to query against your local wikimeda install.  Would recommend you use a backup of the simple wiki w/o images for space.  

# running locally

On a stretch pi....
```
apt install apache2 php php7.0-sqlite php-mbstring -y

```
may be 7.1 on some distros

Next up, install mediawiki
https://www.mediawiki.org/wiki/Manual:Installation_guide

```
cd  /opt/
wget https://releases.wikimedia.org/mediawiki/1.31/mediawiki-1.31.0.tar.gz
tar xfz mediawiki-1.31.0.tar.gz
cp -R mediawiki-1.31.0 wiktionary
cd /var/www/html
ln -s /opt/mediawiki-1.31.0 localwiki
ln -s /opt/wiktionary wiktionary
cd /opt
mkdir sqlite
cd sqlite
mkdir wikipedia
mkdir wiktionary

```
Now, run through setup for each of these, pointing the sqlite location to the relevant location (/opt/sqlite/wikipedia or /opt/sqlite/wiktionary) 

After setup is completed and you get the generic welcome message on the site, import some data...and wait forever.  Do these under screen for your own sanity

```
cd /tmp
wget https://dumps.wikimedia.org/simplewiki/20180901/simplewiki-20180901-pages-articles-multistream.xml.bz2
bunzip simplewiki-20180901-pages-articles-multistream.xml.bz2
#### this takes a long time, run under screen
screen -S import
cd opt/mediawiki-1.31.0
php maintenance/importDump.php < /tmp/simplewiki-20180901-pages-articles-multistream.xml

```
...this goes like 1.3 pg/sec on a pi3.  160k pages.  

for wiktionary it's also really slow:

```
cd /tmp/
wget https://dumps.wikimedia.org/enwiktionary/20180901/enwiktionary-20180901-pages-articles-multistream.xml.bz2
bunzip enwiktionary-20180901-pages-articles-multistream.xml.bz2
cd opt/wiktionary
php maintenance/importDump.php < /tmp/enwiktionary-20180901-pages-articles-multistream.xml

```
After all this finishes, restart apache and navigate to: http://pi-ip-or-name/localwiki and verify it works as expected.
