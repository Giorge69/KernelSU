kernelsu-objs := ksu.o
kernelsu-objs += allowlist.o
kernelsu-objs += apk_sign.o
kernelsu-objs += sucompat.o
kernelsu-objs += throne_tracker.o
kernelsu-objs += core_hook.o
kernelsu-objs += ksud.o
kernelsu-objs += embed_ksud.o
kernelsu-objs += kernel_compat.o

kernelsu-objs += selinux/selinux.o
kernelsu-objs += selinux/sepolicy.o
kernelsu-objs += selinux/rules.o
ccflags-y += -I$(srctree)/security/selinux -I$(srctree)/security/selinux/include
ccflags-y += -I$(objtree)/security/selinux -include $(srctree)/include/uapi/asm-generic/errno.h

obj-$(CONFIG_KSU) += kernelsu.o

# .git is a text file while the module is imported by 'git submodule add'.
ifeq ($(shell test -e $(srctree)/$(src)/../.git; echo $$?),0)
$(shell cd $(srctree)/$(src); /usr/bin/env PATH="$$PATH":/usr/bin:/usr/local/bin [ -f ../.git/shallow ] && git fetch --unshallow)
KSU_GIT_VERSION := $(shell cd $(srctree)/$(src); /usr/bin/env PATH="$$PATH":/usr/bin:/usr/local/bin git rev-list --count HEAD)
# ksu_version: major * 10000 + git version + 200 for historical reasons
$(eval KSU_VERSION=$(shell expr 10000 + $(KSU_GIT_VERSION) + 165))
$(info -- KernelSU version: $(KSU_VERSION))
ccflags-y += -DKSU_VERSION=$(KSU_VERSION)
else # If there is no .git file, the default version will be passed.
$(warning "KSU_GIT_VERSION not defined! It is better to make KernelSU a git submodule!")
ccflags-y += -DKSU_VERSION=16
endif

ifeq ($(shell grep -q " current_sid(void)" $(srctree)/security/selinux/include/objsec.h; echo $$?),0)
ccflags-y += -DKSU_COMPAT_HAS_CURRENT_SID
endif

ifeq ($(shell grep -q "struct selinux_state " $(srctree)/security/selinux/include/security.h; echo $$?),0)
ccflags-y += -DKSU_COMPAT_HAS_SELINUX_STATE
endif

ifndef KSU_EXPECTED_SIZE
KSU_EXPECTED_SIZE := 0x363
endif

ifndef KSU_EXPECTED_HASH
KSU_EXPECTED_HASH := 4359c171f32543394cbc23ef908c4bb94cad7c8087002ba164c8230948c21549
endif

ifdef KSU_MANAGER_PACKAGE
ccflags-y += -DKSU_MANAGER_PACKAGE=\"$(KSU_MANAGER_PACKAGE)\"
$(info -- KernelSU Manager package name: $(KSU_MANAGER_PACKAGE))
endif

$(info -- KernelSU Manager signature size: $(KSU_EXPECTED_SIZE))
$(info -- KernelSU Manager signature hash: $(KSU_EXPECTED_HASH))

ccflags-y += -DEXPECTED_SIZE=$(KSU_EXPECTED_SIZE)
ccflags-y += -DEXPECTED_HASH=\"$(KSU_EXPECTED_HASH)\"

ifeq ($(shell grep -q "int path_umount" $(srctree)/fs/namespace.c; echo $$?),0)
ccflags-y += -DKSU_UMOUNT
endif

ccflags-y += -Wno-implicit-function-declaration -Wno-strict-prototypes -Wno-int-conversion -Wno-gcc-compat
ccflags-y += -Wno-declaration-after-statement -Wno-unused-function

ifeq ($(shell test -e $(srctree)/fs/susfs.c; echo $$?),0)
ifdef KSU_SUSFS
ccflags-y += -DKSU_SUSFS
endif
ifdef KSU_SUSFS_SUS_PATH
ccflags-y += -DKSU_SUSFS_SUS_PATH
endif
ifdef KSU_SUSFS_SUS_MOUNT
ccflags-y += -DKSU_SUSFS_SUS_MOUNT
endif
ifdef KSU_SUSFS_AUTO_ADD_SUS_KSU_DEFAULT_MOUNT
ccflags-y += -DKSU_SUSFS_AUTO_ADD_SUS_KSU_DEFAULT_MOUNT
endif
ifdef KSU_SUSFS_AUTO_ADD_SUS_BIND_MOUNT
ccflags-y += -DKSU_SUSFS_AUTO_ADD_SUS_BIND_MOUNT
endif
ifdef KSU_SUSFS_SUS_KSTAT
ccflags-y += -DKSU_SUSFS_SUS_KSTAT
endif
ifdef KSU_SUSFS_SUS_OVERLAYFS
ccflags-y += -DKSU_SUSFS_SUS_OVERLAYFS
endif
ifdef KSU_SUSFS_TRY_UMOUNT
ccflags-y += -DKSU_SUSFS_TRY_UMOUNT
endif
ifdef KSU_SUSFS_AUTO_ADD_TRY_UMOUNT_FOR_BIND_MOUNT
ccflags-y += -DKSU_SUSFS_AUTO_ADD_TRY_UMOUNT_FOR_BIND_MOUNT
endif
ifdef KSU_SUSFS_SPOOF_UNAME
ccflags-y += -DKSU_SUSFS_SPOOF_UNAME
endif
ifdef KSU_SUSFS_ENABLE_LOG
ccflags-y += -DKSU_SUSFS_ENABLE_LOG
endif
ifdef KSU_SUSFS_HIDE_KSU_SUSFS_SYMBOLS
ccflags-y += -DKSU_SUSFS_HIDE_KSU_SUSFS_SYMBOLS
endif
ifdef KSU_SUSFS_SPOOF_PROC_CMDLINE
ccflags-y += -DKSU_SUSFS_SPOOF_PROC_CMDLINE
endif
ifdef KSU_SUSFS_OPEN_REDIRECT
ccflags-y += -DKSU_SUSFS_OPEN_REDIRECT
endif
ifdef KSU_SUSFS_SUS_SU
ccflags-y += -DKSU_SUSFS_SUS_SU
endif
else
$(info -- You have not integrate susfs in your kernel.)
$(info -- Read: https://gitlab.com/simonpunk/susfs4ksu)
endif

# Keep a new line here!! Because someone may append config
