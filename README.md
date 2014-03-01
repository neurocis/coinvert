coinvert
========

coinvert facilitates easy switching of cgminer flavoured cryptocoin mining programs on linux (BAMT).

Authored by neurocis <neurocis@gmail.com>, released under GPLv3
BTC: 1DDqzhbbeKCm84SffUsDqbYH5FhGcJ33dy


Motivation:

I was getting tired of the BAMT flavor of the month club and wanted to be able to mine any algorythm type and easily switch between them while preserving the core functionality of BAMT itself.


Usage:

coinvert has 4 subcommands; install, link, mine and make.

    install {miner}

Downloads from GIT url, configures and makes executable in /opt/miners by default, where {miner} can be:

    cgminer-ckolivas
    sgminer
    vertminer
    vertminer-thekev
    cgminer-reorder

Also touches a file name .{algoryth} in the installation directory which identifies its algoryth type. If you have an existing miner you wish to link in coinvert, you will need to touch this file as well or it will not recognize it.

    link {algorythm} {miner}

Creates a symlink representing the algorythm the miner is used for and the installed directory, typically you would link scrypt to either cgminer-ckolivas or sgminer, and nscrypt to vertminer or vertminer-thekev lastly keccak to cgminer-reorder.

    mine {algorythm}|autoapi

Links up cgminer to point at your preferred miner for the specified algoryth and stops and restarts the miner. In the case of autoapi it will retrieve a JSON from a URL specified in the script (look near the top) and parse it based on a jq query for profitability, then if difference is greater than defined delta will relink to that type/miner.
    
Also links cgminer.conf to point to one specific to that algorythm, on bamt these are named /etc/bamt/algo-{algorythm}.conf.

coinvert mine auto-api can be placed in a cron job to poll and switch the miner as needed unattended.

    make {git_url} {target_dir} {executable_name} {algorythm_name} {configure_parameters}

See script for operation, but is called by install to clone, configure and make a miner.

Installation:

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

