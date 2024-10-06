# SPDX-License-Identifier: (GPL-2.0 OR BSD-2-Clause)

XDP_TARGETS  := xdp_prog_kern
USER_TARGETS := xdp_prog_user

COMMON_DIR := ../common

COPY_LOADER := xdp-loader
COPY_STATS  := xdp_stats
EXTRA_DEPS := $(COMMON_DIR)/parsing_helpers.h
COMMON_OBJS := $(COMMON_DIR)/common_user_bpf_xdp.o

include $(COMMON_DIR)/common.mk

assignment1: reset xdp_prog_kern.o
	sudo mount -t bpf bpf /sys/fs/bpf/
	sudo ../testenv/testenv.sh setup -n test --legacy-ip
	sudo ../testenv/testenv.sh reset -n test --legacy-ip # to be sure
	sleep 1
	sudo ../testenv/testenv.sh exec -n test -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	# sudo ../testenv/testenv.sh load -n test -- --prog-name xdp_icmp_echo_func xdp_prog_kern.o
	sudo ./xdp-loader load -n xdp_icmp_echo_func -p /sys/fs/bpf/test test xdp_prog_kern.o
	timeout 6 sudo ../testenv/testenv.sh ping >/dev/null & 
	timeout 6 sudo ../testenv/testenv.sh ping --legacy-ip >/dev/null &
	timeout 5 sudo ./xdp_stats -d test 

assignment2: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup -n left --legacy-ip
	sudo ../testenv/testenv.sh setup -n right --legacy-ip
	sleep 1
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/right right xdp_prog_kern.o
	sudo ../testenv/testenv.sh exec -n left -- sudo ./xdp-loader load --prog-name xdp_pass_func -p /sys/fs/bpf/left veth0 xdp_prog_kern.o
	# 
	timeout 6 sudo ../testenv/testenv.sh exec -n right -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n left -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d right

assignment3: reset xdp_prog_kern.o
	sudo ../testenv/testenv.sh setup -n left --legacy-ip
	sudo ../testenv/testenv.sh setup -n right --legacy-ip
	sleep 1
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/left left xdp_prog_kern.o
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/right right xdp_prog_kern.o
	#
	sudo ../testenv/testenv.sh exec -n left -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	sudo ../testenv/testenv.sh exec -n right -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	sudo ../testenv/testenv.sh redirect right left
	#
	echo "TEST LEFT"
	timeout 6 sudo ../testenv/testenv.sh exec -n left -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n left -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d left
	# 
	echo "TEST RIGHT"
	timeout 6 sudo ../testenv/testenv.sh exec -n right -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n right -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d right

assignment4: reset xdp_prog_kern.o
	sudo rm -rf /sys/fs/bpf/uno
	sudo rm -rf /sys/fs/bpf/dos
	sudo rm -rf /sys/fs/bpf/tres
	sudo ./xdp-loader unload uno --all
	sudo ./xdp-loader unload dos --all
	sudo ./xdp-loader unload tres --all
	sudo sysctl net.ipv4.conf.all.forwarding=1
	sudo sysctl net.ipv6.conf.all.forwarding=1
	#
	sudo ../testenv/testenv.sh setup -n uno --legacy-ip
	sudo ../testenv/testenv.sh setup -n dos --legacy-ip
	sudo ../testenv/testenv.sh setup -n tres --legacy-ip
	#
	sleep 1
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/uno uno xdp_prog_kern.o
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/dos dos xdp_prog_kern.o
	sudo ./xdp-loader load -n xdp_redirect_func -p /sys/fs/bpf/tres tres xdp_prog_kern.o
	# 
	sudo ../testenv/testenv.sh exec -n uno -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	sudo ../testenv/testenv.sh exec -n dos -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	sudo ../testenv/testenv.sh exec -n tres -- sudo ./xdp-loader load --prog-name xdp_pass_func veth0 xdp_prog_kern.o
	#
	echo "TEST UNO"
	timeout 6 sudo ../testenv/testenv.sh exec -n uno -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n uno -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d uno 
	echo "TEST DOS"
	timeout 6 sudo ../testenv/testenv.sh exec -n dos -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n dos -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d dos 
	echo "TEST TRES"
	timeout 6 sudo ../testenv/testenv.sh exec -n tres -- ping fc00:dead:cafe:1::1 >/dev/null &
	timeout 5 sudo ../testenv/testenv.sh exec -n tres -- tcpdump -l &
	timeout 5 sudo ./xdp_stats -d tres

status:
	sudo ./xdp-loader status

reset:
	sudo rm -rf /sys/fs/bpf/left
	sudo rm -rf /sys/fs/bpf/right
	sudo ./xdp-loader unload test --all
	sudo ./xdp-loader unload left --all
	sudo ./xdp-loader unload right --all


