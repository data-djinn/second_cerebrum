[[linux]] [[bash]]
# Downloading data using curl
==unix command line tool for transfering data to and from a server==
- used to download data from HTTPS sites & FTP servers
- use `curs -o filename https://url.com` to save as filename, otherwise `-O` saves it as OG filename
- use `*` as wildcard operator for url address
- use `[001-100:10]` to "glob" all ints in range in increments of 10
### curl flags:
- `-L` redirects HTTP URL if 300 error code occurs
- `-C` resumes a previous file transfer if it times out before completion

# wget
- compatible for all operating systoms
- downloads data from HTTPS & FTP
- better than `curl` at downloading multiple files recursively
### wget flags
`-b` download in background
`-q` turn off wget output
`-c` resume broken download
`-i` pass a list of file locations in a text file
`--limit-rate=={}k` limit bandwidth in bytes per second
`--wait={}` sets time to wait in between files
```
# Use curl, download and rename a single file from URL
curl -o Spotify201812.zip -L https://assets.datacamp.com/production/repositories/4180/datasets/eb1d6a36fa3039e4e00064797e1a1600d267b135/201812SpotifyData.zip

# Unzip, delete, then re-name to Spotify201812.csv
unzip Spotify201812.zip && rm Spotify201812.zip
mv 201812SpotifyData.csv Spotify201812.csv

# View url_list.txt to verify content
cat url_list.txt

# Use Wget, limit the download rate to 2500 KB/s, download all files in url_list.txt
wget --limit-rate=2500k -i url_list.txt

# Take a look at all files downloaded
ls
```