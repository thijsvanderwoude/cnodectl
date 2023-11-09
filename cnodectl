#!/bin/sh
error=0

cardano_node_enable=$(sysrc -n cardano_node_enable-"NO")
if [ "$cardano_node_enable" = "NO" ]
then
    echo "Error: node not enabled!"
    error=1
fi

# Defaults taken from https://github.com/freebsd/freebsd-ports-haskell/blob/main/net-p2p/cardano-node/files/cardano_node.in
cardano_node_home=$(sysrc -n cardano_node_home-"/var/db/cardano_node")
cardano_node_socket=$(sysrc -n cardano_node_socket-"${cardano_node_home}/cardano-node.sock")

case $1 in
    # Gets the time the node has been running.
    uptime)
	pid=$(pgrep cardano-node)
        if [ -z "$pid" ]
        then
            echo "Error: can't find pid of cardano-node!"
            echo "Try running with root privileges."
            error=1
        else
            ps -p $(pgrep cardano-node) -o etime
            error=$?
        fi
    ;;
    # Finds the nodes current tip of blockchain.
    tip)
        cardano-cli query tip --socket-path="$cardano_node_socket" --mainnet
        error=$?
    ;;
    start|status|stop|restart)
        service cardano_node "$1"
    ;;
    # Gives a journalctl-esque live view of the node logs.
    journal)
        eval "tail -f $cardano_node_home/logs/cnode.json | ccze -A"
    ;;
    help|*)
        echo -e "Usage: $0 help|journal|restart|start|status|stop|tip|uptime\n"
        echo "Options:"
        echo "  help       display this help page"
        echo "  journal    a journalctl-esque live view of the node logs"
        echo "  restart    restart the node"
        echo "  start      start the node"
        echo "  status     display node status"
        echo "  stop       stop the node"
        echo "  tip        displays the current tip of the node"
        echo "  uptime     displays node uptime in ps -o etime format"
        error=1
    ;;
esac

exit $error
