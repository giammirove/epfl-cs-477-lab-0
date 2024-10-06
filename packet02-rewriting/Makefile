# SPDX-License-Identifier: (GPL-2.0 OR BSD-2-Clause)

XDP_TARGETS  := xdp_prog_kern
USER_TARGETS :=

COMMON_DIR := ../common

COPY_LOADER := xdp-loader
COPY_STATS  := xdp_stats
EXTRA_DEPS := $(COMMON_DIR)/parsing_helpers.h

include $(COMMON_DIR)/common.mk

assignment1: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02
	sudo ./xdp-loader load -n xdp_port_rewrite_func veth-basic02 xdp_prog_kern.o
	timeout 5 sudo ../testenv/testenv.sh tcpdump &
	sleep 1
	timeout 1 echo "1" | sudo ../testenv/testenv.sh exec -- socat - 'udp6:[fc00:dead:cafe:1::1]:2000' 
	timeout 1 echo "2" | sudo ../testenv/testenv.sh exec -- socat - 'udp6:[fc00:dead:cafe:1::1]:2000'
	timeout 1 echo "3" | sudo ../testenv/testenv.sh exec -- socat - 'udp6:[fc00:dead:cafe:1::1]:2000' 
	
assignment2: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02
	sudo ../testenv/testenv.sh reset --vlan
	sudo ./xdp-loader load -n xdp_vlan_swap_func veth-basic02 xdp_prog_kern.o
	timeout 6 sudo ../testenv/testenv.sh tcpdump &
	sleep 1
	timeout 5 sudo ../testenv/testenv.sh ping --vlan --name veth-basic02

assignment3: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02
	sudo ../testenv/testenv.sh reset --vlan
	sudo ./xdp-loader load -n xdp_vlan_swap_func veth-basic02 xdp_prog_kern.o
	timeout 6 sudo ../testenv/testenv.sh tcpdump &
	sleep 1
	timeout 5 sudo ../testenv/testenv.sh ping --name veth-basic02

status:
	sudo ./xdp-loader status

reset:
	sudo ./xdp-loader unload veth-basic02 --all

