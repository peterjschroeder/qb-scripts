#!/usr/bin/python3
import io, json, os, shlex, subprocess, sys, validators
from xdg.BaseDirectory import *

def check_parent(bid, sid):
    b = []
    out_str = 'ffsclient bookmarks list --type="folder" --parent="%s" --no-table-truncate --table-columns="id,title"' % bid
    output = subprocess.check_output(shlex.split(out_str), encoding="utf-8").split('\n')

    for num, i in enumerate(output):
        if num == 0 or num == 1 or num == len(output)-1:
            continue
        b.append([i[:12], i[16:].strip()])

    for j in b:
        if sid in j[0]:
            return True

    return False

def bookmark_add(title, url, bpath=''):
    bid = ''
    bookmark_folders = []

    # Load all folders into a list
    output = subprocess.check_output(shlex.split('ffsclient bookmarks list --type="folder" --no-table-truncate --table-columns="id,title"'), encoding="utf-8").split('\n')

    for num, i in enumerate(output):
        if num == 0 or num == 1 or num == len(output)-1:
            continue
        bookmark_folders.append([i[:12], i[16:].strip()])

    # Create missing folders
    mid = 'menu'
    for i in bpath.split('/'):
        mcreate = True
        for j in bookmark_folders:
            if i in j[1]:
                mcreate = False
                mid = j[0]

        if mcreate:
            out_str = 'ffsclient bookmarks create folder %s --parent %s' % (i, mid)
            mid = subprocess.check_output(shlex.split(out_str), encoding="utf-8").strip()
            bookmark_folders.append([mid, i])

    # Create the bookmark
    if bpath:
        for i in bpath.split('/'):
            for j in bookmark_folders:
                if i in j[1]:
                    if not bid or check_parent(bid, j[0]):
                        bid = j[0]

    if not bid:
        out_str = 'ffsclient bookmarks create bookmark "%s" %s' % (title, url)
    else:
        out_str = 'ffsclient bookmarks create bookmark "%s" %s --parent %s' % (title, url, bid)
    subprocess.run(shlex.split(out_str))

def bookmark_delete(bookmark):
    b = []

    with open(os.path.join(xdg_config_home, "qutebrowser", "bookmarks", "urls"), 'w') as f:
        out_str = 'ffsclient bookmarks list --type="bookmark" --no-table-truncate --table-columns="id,uri"'
        output = subprocess.check_output(shlex.split(out_str), encoding="utf-8").split('\n')

        for num, i in enumerate(output):
            if num == 0 or num == 1 or num == len(output)-1:
                continue
            b.append([i[:12].strip(), i[16:].strip()])

        print (b)

        for i in b:
            if bookmark == i[1]:
                out_str = 'ffsclient bookmarks delete %s' % i[0]
                output = subprocess.check_output(shlex.split(out_str), encoding="utf-8")
                return

# FIXME: Force qutebrowser to reload bookmarks
def bookmark_list():
    with open(os.path.join(xdg_config_home, "qutebrowser", "bookmarks", "urls"), 'w') as f:
        out_str = 'ffsclient bookmarks list --type="bookmark" --no-table-truncate --table-columns="uri,title"'
        output = subprocess.check_output(shlex.split(out_str), encoding="utf-8").split('\n')

        for num, i in enumerate(output):
            if num == 0 or num == 1 or num == len(output)-1:
                continue
            f.write('%s %s\n' % (i.split(' ', 1)[0].strip(), i.split(' ', 1)[1].strip()))

def main():
    if len(sys.argv) == 1:
        print('qbfsync <add/del/list>')
    else:
        if sys.argv[1] == 'add':
            if len(sys.argv) < 4:
                print('qbfsync add <title> <url> <path>')
                return
            else:
                if not validators.url(sys.argv[3]):
                    print('Invalid url')
                    return
                if len(sys.argv) < 5:
                    bookmark_add(sys.argv[2], sys.argv[3])
                else:
                    bookmark_add(sys.argv[2], sys.argv[3], sys.argv[4].strip("/"))
        elif sys.argv[1] == 'del' or sys.argv[1] == 'delete':
            if len(sys.argv) < 3:
                print('qbfsync del <bookmark>')
                return
            else:
                bookmark_delete(sys.argv[2])
        elif sys.argv[1] == 'list':
            bookmark_list()
if __name__ == "__main__":
    main()

