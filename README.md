# Mycroft with Local Wikipedia 

Alter your mycroft system's copy of the wikipedia python package to update the API_URL to query against your local wikimeda install, should be found in /usr/local/lib/python3.5/dist-packages/wikipedia/wikipedia.py  

Would recommend you use a backup of the simple wiki w/o images for space.  

# running locally

On a stretch pi....most of these can be adjusted for other distros pretty easily. Also works with lighttpd. 

```
sudo apt install apache2 php php7.0-sqlite php-mbstring -y

```
may be 7.1 on other distros, no biggie.

Next up, install mediawiki
https://www.mediawiki.org/wiki/Manual:Installation_guide

```
sudo -s
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
sudo php maintenance/importDump.php < /tmp/simplewiki-20180901-pages-articles-multistream.xml

```
...this goes like .8 pg/sec on a pi3.  160k pages. It's two and a half days.  

For wiktionary it's even slower (and also a much bigger file)!

```
cd /tmp/
wget https://dumps.wikimedia.org/enwiktionary/20180901/enwiktionary-20180901-pages-articles-multistream.xml.bz2
bunzip enwiktionary-20180901-pages-articles-multistream.xml.bz2
cd opt/wiktionary
sudo php maintenance/importDump.php < /tmp/enwiktionary-20180901-pages-articles-multistream.xml

```
After all this finishes, restart apache and navigate to: http://pi-ip-or-name/localwiki and verify it works as expected.
