coinvert
========

Motivation:
===========

I was getting tired of the BAMT flavor of the month club and wanted to be able to mine any algorythm type and easily switch between them while preserving the core functionality of BAMT itself.

Authored by neurocis <neurocis@gmail.com>, released under GPLv3.
BTC: 1DDqzhbbeKCm84SffUsDqbYH5FhGcJ33dy


Usage:
======

coinvert has 4 subcommands; install, link, mine and make.

    coinvert install {miner}

Downloads from GIT url, configures and makes executable in /opt/miners by default, where {miner} can be:

    coinvert install cgminer-ckolivas
    coinvert install sgminer
    coinvert install vertminer
    coinvert install vertminer-thekev
    coinvert install cgminer-reorder

Also touches a file name .{algoryth} in the installation directory which identifies its algoryth type. If you have an existing miner you wish to link in coinvert, you will need to touch this file as well or it will not recognize its algorythm.

    coinvert link {algorythm} {miner}

Creates a symlink representing the algorythm the miner is used for and the installed directory, typically you would link scrypt to either cgminer-ckolivas or sgminer, and nscrypt to vertminer or vertminer-thekev lastly keccak to cgminer-reorder.

    coinvert mine {algorythm}|autoapi

Links up cgminer to point at your preferred miner for the specified algoryth and stops and restarts the miner. In the case of autoapi it will retrieve a JSON from a URL specified in the script (look near the top) and parse it based on a jq query for profitability, then if difference is greater than defined delta will relink to that type/miner.
    
Also links cgminer.conf to point to one specific to that algorythm, on bamt these are named /etc/bamt/algo-{algorythm}.conf.

coinvert mine auto-api can be placed in a cron job to poll and switch the miner as needed unattended.

    coinvert make {git_url} {target_dir} {executable_name} {algorythm_name} {configure_parameters}

See script for operation, but is called by install to clone, configure and make a miner.


Installation:
=============

    sudo su
    #
    # Depends on curl to grab aloryth score via JSON
    apt-get install curl
    #
    # Depends on jq to parse JSON
    # http://stedolan.github.io/jq/
    cd /usr/bin
    wget http://stedolan.github.io/jq/download/linux32/jq
    chmod +x jq
    #
    # coinvert itself
    cd /opt
    git clone https://github.com/neurocis/coinvert.git
    chmod +x /opt/coinvert/coinvert
    ln -s /opt/coinvert/coinvert /usr/sbin/coinvert


Sample:
=======

    root@bamt-miner:~# coinvert install sgminer
    {Goes through clone, configure, make}
    root@bamt-miner:~# coinvert install vertminer
    {Goes through clone, configure, make}
    root@bamt-miner:~# coinvert link scrypt sgminer
    2014-03-01 08:10:07+00:00 [INFO] Linked /opt/miners/algo-scrypt to /opt/miners/sgminer
    root@bamt-miner:~# coinvert link nscrypt vertminer
    2014-03-01 08:10:21+00:00 [INFO] Linked /opt/miners/algo-nscrypt to /opt/miners/vertminer
    root@bamt-miner:~# coinvert mine nscrypt
    2014-03-01 08:10:53+00:00 [INFO] Linked /opt/miners/cgminer and /etc/bamt/cgminer.conf to mine nscrypt.
    2014-03-01 08:10:56+00:00 [INFO] Killing miner process 32054
    2014-03-01 08:11:01+00:00 [INFO] Miner started (PID: 13696)
    root@bamt-miner:~# coinvert mine scrypt
    2014-03-01 08:11:05+00:00 [INFO] Linked /opt/miners/cgminer and /etc/bamt/cgminer.conf to mine scrypt.
    2014-03-01 08:11:08+00:00 [INFO] Killing miner process 13696
    2014-03-01 08:11:13+00:00 [INFO] Miner started (PID: 13822)
    root@bamt-miner:~# coinvert mine autoapi
    2014-03-01 08:11:34+00:00 [INFO] Retrieving algorythm scores from https://xxxxxxxx/api/bestalgo?key=xxxx
    2014-03-01 08:11:34+00:00 [INFO] Score: 142.86579 - nscrypt
    2014-03-01 08:11:34+00:00 [INFO] Score: 111.01208 - scrypt
    2014-03-01 08:11:34+00:00 [INFO] Delta is greater than 25 - Coinverting to nscrypt.
    2014-03-01 08:11:35+00:00 [INFO] Linked /opt/miners/cgminer and /etc/bamt/cgminer.conf to mine nscrypt.
    2014-03-01 08:11:41+00:00 [INFO] Killing miner process 32054
    2014-03-01 08:11:46+00:00 [INFO] Miner started (PID: 3498)
    root@bamt-miner:~# coinvert mine scrypt
    2014-03-01 08:16:41+00:00 [INFO] Currently mining scrypt, nothing to do.
    root@bamt-miner:~#
