
# Gogs

[Gogs](https://gogs.io) is an open source git repository server that is very lightweight.

QNAP's Container-Station shows GitLab as one of the easy to install containers.  However,
GitLab is a bit of a pig.  It consumed 25-30% of the memory on my QNAP server.  It launches
3 different containers including postgress and redis.  Gogs on the other hand needs no
other containers (if you use sqllite) and consumes about 1% of the QNAP memory.  If you
are going to install a local git repository on your QNAP server I highly recommend Gogs.
(Despite the very weird name!)

Thankfully getting Gogs to run on QNAP is trivial.  They have a [version](https://github.com/gogits/gogs/tree/master/docker) built in Docker that makes set-up very easy.  

The file **docker-compose.yml** will easily get you going.

Before running though you should consider what ports you want the server to run on.  The 
included compose file maps these to 10022 for ssh and 10080 for http.  You may need to
edit the compose file if you are already using those ports on your QNAP server.

To get things running simply do:
```bash
docker-compose up -d
```

You will then need to go to <your qnap box>:10080 to finish the configuration.  I just
configured the server to use sqllite since this repository is not going to be heavily used.

All data will be stored in the named volume: _gogs_data_

If you ssh to you QNAP box you should be able to find it here:
```bash
/share/Container/container-station-data/lib/docker/volumes/gogs_data/_data
```

This can be useful if you need to edit the ini file by hand in gogs/conf/app.ini
