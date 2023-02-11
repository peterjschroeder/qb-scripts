#!/usr/bin/python3
 
import re
import requests
import sys
from bs4 import BeautifulSoup
from os import environ
from pathlib import Path
from urllib.parse import urljoin

def qexec(cmd):
    with open(environ['QUTE_FIFO'], 'w') as f:
        f.write(cmd)
        return

if __name__ == '__main__':
    url = environ['QUTE_URL']
    r = requests.get(url, headers={'User-agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:50.0) Gecko/20100101 Firefox/50.0'})
    soup = BeautifulSoup(r.text, "lxml")
    
    if len(sys.argv) < 2:
        qexec('message-info "Usage: downloadAll [Regex]."')
        sys.exit(0)
        
    if not (url.startswith("http")):
        sys.exit(0)
                
    for tag in soup.findAll('a', href=True):
        tag['href'] = urljoin(url, tag['href'])

        try:
            if re.search(sys.argv[1], tag['href']):
                qexec("download --dest " + environ['QUTE_DOWNLOAD_DIR'] + " " + tag['href'] + "\n")
        except:
            continue
