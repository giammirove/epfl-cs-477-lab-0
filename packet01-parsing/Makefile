# SPDX-License-Identifier: (GPL-2.0 OR BSD-2-Clause)

XDP_TARGETS  := xdp_prog_kern
USER_TARGETS :=

COMMON_DIR := ../common

COPY_LOADER := xdp-loader
COPY_STATS  := xdp_stats

include $(COMMON_DIR)/common.mk

assignment3: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02
	sudo ./xdp-loader load -n xdp_parser_func veth-basic02 xdp_prog_kern.o
	sudo ../testenv/testenv.sh ping --name veth-basic02
	

assignment4: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02
	sudo ../testenv/testenv.sh reset --vlan
	sudo ./xdp-loader load -n xdp_parser_func veth-basic02 xdp_prog_kern.o
	sudo ../testenv/testenv.sh ping --vlan --name veth-basic02

assignment5: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup --name veth-basic02 --legacy-ip
	sudo ../testenv/testenv.sh reset --vlan --legacy-ip
	sudo ./xdp-loader load -n xdp_parser_func veth-basic02 xdp_prog_kern.o
	sudo ../testenv/testenv.sh ping --legacy-ip --name veth-basic02

status:
	sudo ./xdp-loader status

reset:
	sudo ./xdp-loader unload veth-basic02 --all
