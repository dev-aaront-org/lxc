# SPDX-License-Identifier: LGPL-2.1-or-later

project('lxc', 'c',
        version : '4.0.0',
        license : 'LGPLv2+',
        default_options: [
		'b_lto=true',
		'b_lto_mode=thin',
		'b_colorout=always',
		'b_asneeded=true',
		'b_pie=true',
		'b_staticpic=true',
                'c_std=gnu11',
                'warning_level=2',
        ],
        meson_version : '>= 0.48',
       )

liblxc_version = '4.0.0'

conf = configuration_data()
conf.set_quoted('PROJECT_URL', 'https://linuxcontainers.org/lxc/introduction/')
conf.set('PROJECT_VERSION',    meson.project_version(),
         description : 'Numerical project version (used where a simple number is expected)')
conf.set('PACKAGE_VERSION',    meson.project_version())
conf.set('_GNU_SOURCE', true)
conf.set('__STDC_FORMAT_MACROS', true)

version_data = configuration_data()
version_data.set('LXC_VERSION_MAJOR', '4')
version_data.set('LXC_VERSION_MINOR', '0')
version_data.set('LXC_VERSION_MICRO', '7')
version_data.set('LXC_ABI', '4.0.7')
version_data.set('LXC_DEVEL', '1')
version_data.set('LXC_VERSION', '4.0.7-devel')

project_source_root = meson.current_source_dir()
project_build_root = meson.current_build_dir()

# join_paths ignores the preceding arguments if an absolute component is
# encountered, so this should canonicalize various paths when they are
# absolute or relative.
prefixdir = get_option('prefix')
bindir = join_paths(prefixdir, get_option('bindir'))
datadir = join_paths(prefixdir, get_option('datadir'))
docdir = join_paths(prefixdir, get_option('docdir'))
includedir = join_paths(prefixdir, get_option('includedir'))
libdir = join_paths(prefixdir, get_option('libdir'))
libexecdir = join_paths(prefixdir, get_option('libexecdir'))
localstatedir = join_paths('/', get_option('localstatedir'))
sbindir = join_paths(prefixdir, get_option('sbindir'))
sysconfdir = join_paths(prefixdir, get_option('sysconfdir'))

apparmorcachedir = get_option('apparmor-cache-dir')
cgrouppattern = get_option('cgroup-pattern')
globalconfig = get_option('global-config')

logpath = get_option('log-path')
lxcpathprefix = get_option('config-path')
rootfsmount = get_option('rootfs-mount-dir')
runtimepath = join_paths(prefixdir, get_option('runtime-path'))

conf.set_quoted('BINDIR',			bindir)
conf.set_quoted('DATADIR',			datadir)
conf.set_quoted('DOCDIR',			docdir)
conf.set_quoted('INCLUDEDIR',			includedir)
conf.set_quoted('LOCALSTATEDIR',		localstatedir)
conf.set_quoted('LIBDIR',			libdir)
conf.set_quoted('LIBEXECDIR',			libexecdir)
conf.set_quoted('SBINDIR',			sbindir)
conf.set_quoted('SYSCONFDIR',			sysconfdir)

conf.set_quoted('LXCINITDIR',			libexecdir)
conf.set_quoted('DEFAULT_CGROUP_PATTERN',	cgrouppattern)
conf.set_quoted('RUNTIME_PATH',			runtimepath)

lxcdefaultconfig = join_paths(sysconfdir, 'lxc/default.conf')
conf.set_quoted('LXC_DEFAULT_CONFIG',		lxcdefaultconfig)

lxcapparmorcachedir = join_paths(localstatedir, apparmorcachedir)
conf.set_quoted('APPARMOR_CACHE_DIR',		lxcapparmorcachedir)

lxcrootfsmount = join_paths(prefixdir, rootfsmount)
conf.set_quoted('LXCROOTFSMOUNT',		lxcrootfsmount)

lxcglobalconfig = join_paths(sysconfdir, globalconfig)
conf.set_quoted('LXC_GLOBAL_CONF',		lxcglobalconfig)

lxclogpath = join_paths(localstatedir, logpath)
conf.set_quoted('LOGPATH',			lxclogpath)

lxcpath = join_paths(localstatedir, lxcpathprefix)
conf.set_quoted('LXCPATH',			lxcpath)

lxctemplateconfig = join_paths(datadir, 'lxc/config')
conf.set_quoted('LXCTEMPLATECONFIG',		lxctemplateconfig)

lxctemplatedir = join_paths(datadir, 'lxc/templates')
conf.set_quoted('LXCTEMPLATEDIR',		lxctemplatedir)

lxchookdir = join_paths(datadir, 'lxc/hooks')
conf.set_quoted('LXCHOOKDIR',			lxchookdir)

lxchookbindir = join_paths(libexecdir, 'lxc/hooks')
conf.set_quoted('LXCBINHOOKDIR',		lxchookbindir)

user_network_conf_opt = get_option('user-network-conf')
lxc_user_network_conf = join_paths(sysconfdir, user_network_conf_opt)
conf.set_quoted('LXC_USERNIC_CONF',		lxc_user_network_conf)

user_network_db_opt = get_option('user-network-db')
lxc_user_network_db = join_paths(runtimepath, user_network_db_opt)
conf.set_quoted('LXC_USERNIC_DB',		lxc_user_network_db)

# AS_AC_EXPAND(LXC_GENERATE_DATE, "$(date --utc --date=@${SOURCE_DATE_EPOCH:-$(date +%s)} '+%Y-%m-%d')")
# AS_AC_EXPAND(LXC_USERNIC_DB, "$with_usernic_db")
# AS_AC_EXPAND(LXC_DISTRO_SYSCONF, "$distrosysconf")

cc = meson.get_compiler('c')
pkgconfig = import('pkgconfig')

possible_cc_flags = [
	'-Wvla',
	'-Wimplicit-fallthrough=5',
	'-Wcast-align',
	'-Wstrict-prototypes',
	'-fno-strict-aliasing',
	'-fstack-clash-protection',
	'-fstack-protector-strong',
	'--param=ssp-buffer-size=4',
	'--mcet -fcf-protection',
	'-Werror=implicit-function-declaration',
	'-Wlogical-op',
	'-Wmissing-include-dirs',
	'-Wold-style-definition',
	'-Winit-self',
	'-Wunused-but-set-variable',
	'-Wno-unused-parameter',
	'-Wfloat-equal',
	'-Wsuggest-attribute=noreturn',
	'-Werror=return-type',
	'-Werror=incompatible-pointer-types',
	'-Wformat=2',
	'-Wshadow',
	'-Wendif-labels',
	'-Werror=overflow',
	'-fdiagnostics-show-option',
	'-Werror=shift-count-overflow',
	'-Werror=shift-overflow=2',
	'-Wdate-time',
	'-Wnested-externs',
	'-fasynchronous-unwind-tables',
	'-fexceptions',
	'-Warray-bounds',
	'-Wrestrict',
	'-Wreturn-local-addr',
	'-fsanitize=cfi',
	'-Wstringop-overflow',
]

possible_link_flags = [
	'-Wl,--gc-sections',
	'-Wl,-z,relro',
	'-Wl,-z,now',
	'-Wl,-fuse-ld=gold',
]

if meson.version().version_compare('>=0.46')
	add_project_link_arguments(cc.get_supported_link_arguments(possible_link_flags), language : 'c')
else
	add_project_link_arguments(possible_link_flags, language : 'c')
endif

add_project_arguments(cc.get_supported_arguments(possible_cc_flags), language : 'c')

foreach header : ['sys/resource.h',
                  'sys/memfd.h',
                  'sys/personality.h',
                  'sys/signalfd.h',
                  'sys/timerfd.h',
                  'pty.h',
                  'utmpx.h',
                 ]

        conf.set10('HAVE_' + header.underscorify().to_upper(),
                   cc.has_header(header))
endforeach

decl_headers = '''
#include <uchar.h>
#include <sys/mount.h>
#include <sys/stat.h>
#include <linux/fs.h>
#include <linux/types.h>
#include <linux/openat2.h>
#include <linux/sched.h>
'''

foreach decl : [
                '__aligned_u64',
                'struct mount_attr',
                'struct open_how',
                'struct clone_args',
               ]

        # We get -1 if the size cannot be determined
        if cc.sizeof(decl, prefix : decl_headers, args : '-D_GNU_SOURCE') > 0
		conf.set10('HAVE_' + decl.underscorify().to_upper(), true)
	else
		conf.set10('HAVE_' + decl.underscorify().to_upper(), false)
	endif
endforeach

foreach ident : [
        ['bpf',               	'''#include <sys/syscall.h>
				   #include <unistd.h>'''],
        ['close_range',       	'''#include <unistd.h>'''],
        ['execveat',          	'''#include <unistd.h>'''],
        ['endmntent',         	'''#include <stdio.h>
				   #include <mntent.h>'''],
        ['faccessat',         	'''#include <fcntl.h>
				   #include <unistd.h>'''],
        ['fexecve',          	'''#include <unistd.h>'''],
        ['fgetln',          	'''#include <stdio.h>'''],
        ['fsconfig',		'''#include <sys/mount.h>'''],
        ['fsmount',		'''#include <sys/mount.h>'''],
        ['fsopen',		'''#include <sys/mount.h>'''],
        ['fspick',		'''#include <sys/mount.h>'''],
        ['getgrgid_r',          '''#include <sys/types.h>
				   #include <grp.h>'''],
        ['getline',		'''#include <stdio.h>'''],
        ['getsubopt',		'''#include <stdlib.h>'''],
        ['gettid',            	'''#include <sys/types.h>
				   #include <unistd.h>'''],
        ['hasmntopt',         	'''#include <stdio.h>
				   #include <mntent.h>'''],
        ['kcmp',              	'''#include <linux/kcmp.h>'''],
        ['keyctl',            	'''#include <sys/types.h>
				   #include <keyutils.h>'''],
        ['memfd_create',	'''#include <sys/mman.h>'''],
        ['mount_setattr',	'''#include <sys/mount.h>'''],
        ['move_mount',        	'''#include <sys/mount.h>'''],
        ['openat2',         	'''#include <sys/types.h>
				   #include <sys/stat.h>
				   #include <fctnl.h>'''],
        ['open_tree',         	'''#include <sys/mount.h>'''],
        ['personality',	      	'''#include <sys/personality.h>'''],
        ['pidfd_open',        	'''#include <stdlib.h>
				   #include <unistd.h>
				   #include <signal.h>
				   #include <sys/wait.h>'''],
        ['pidfd_send_signal', 	'''#include <stdlib.h>
				   #include <unistd.h>
				   #include <signal.h>
				   #include <sys/wait.h>'''],
        ['pivot_root',        	'''#include <stdlib.h>
				   #include <unistd.h>'''],     # no known header declares pivot_root
        ['prlimit',	      	'''#include <sys/time.h>
				   #include <sys/resource.h>'''],
        ['prlimit64',	      	'''#include <sys/time.h>
				   #include <sys/resource.h>'''],
        ['renameat2',         	'''#include <stdio.h>
				   #include <fcntl.h>'''],
        ['sethostname',	      	'''#include <unistd.h>'''],
        ['setmntent',         	'''#include <stdio.h>
				   #include <mntent.h>'''],
        ['setns',             	'''#include <sched.h>'''],
        ['signalfd',	      	'''#include <sys/signalfd.h>'''],
        ['statx',             	'''#include <sys/types.h>
				   #include <sys/stat.h>
				   #include <unistd.h>'''],
        ['strchrnul',	      	'''#include <string.h>'''],
        ['strlcat',	      	'''#include <string.h>'''],
        ['strlcpy',	      	'''#include <string.h>'''],
        ['unshare',	      	'''#include <sched.h>'''],
]

        have = cc.has_function(ident[0], prefix : ident[1], args : '-D_GNU_SOURCE')
	conf.set10('HAVE_' + ident[0].to_upper(), have)
endforeach

sh = find_program('sh')
git = find_program('git', required : false)
time_epoch = run_command(sh, '-c', 'echo "$SOURCE_DATE_EPOCH"').stdout().strip()
if time_epoch == '' and git.found() and run_command('test', '-e', '.git').returncode() == 0
        # If we're in a git repository, use the creation time of the latest git tag.
        latest_tag = run_command(git, 'describe', '--abbrev=0', '--tags').stdout().strip()
        time_epoch = run_command(git, 'log', '--no-show-signature', '-1', '--format=%at', latest_tag).stdout()
endif
time_epoch = time_epoch.to_int()
conf.set('TIME_EPOCH', time_epoch)

threads = dependency('threads')
libseccomp = dependency('libseccomp')
conf.set10('HAVE_SECCOMP', libseccomp.found())
if libseccomp.found()
	if libseccomp.version().version_compare('>=2.5.0')
		# https://github.com/seccomp/libseccomp/commit/dead12bc788b259b148cc4d93b970ef0bd602b1a
		conf.set10('HAVE_DECL_SECCOMP_NOTIFY_FD', true)
	else
		conf.set10('HAVE_DECL_SECCOMP_NOTIFY_FD', false)
	endif

	if libseccomp.version().version_compare('>=2.0.0')
		# https://github.com/seccomp/libseccomp/commit/6220c8c0fc479d97b6d3e3166a4e46fbfe25a3c0
		conf.set10('HAVE_DECL_SECCOMP_SYSCALL_RESOLVE_NAME_ARCH', true)
	else
		conf.set10('HAVE_DECL_SECCOMP_SYSCALL_RESOLVE_NAME_ARCH', false)
	endif

	seccomp_headers = '''
	#include <seccomp.h>
	'''
	foreach decl : [
	                'scmp_filter_ctx',
	                'struct seccomp_notif_sizes',
	                'struct clone_args',
	               ]

	        # We get -1 if the size cannot be determined
	        if cc.sizeof(decl, prefix : seccomp_headers, args : '-D_GNU_SOURCE') > 0
			conf.set10('HAVE_' + decl.underscorify().to_upper(), true)
		else
			conf.set10('HAVE_' + decl.underscorify().to_upper(), false)
		endif
	endforeach
endif

libselinux = dependency('libselinux', required : false)
conf.set10('HAVE_SELINUX', libselinux.found())

libapparmor = dependency('libapparmor', required : false)
conf.set10('HAVE_APPARMOR', libapparmor.found())

libopenssl = dependency('openssl', required : false)
conf.set10('HAVE_OPENSSL', libopenssl.found())

libcap = dependency('libcap', required : false)
if not libcap.found()
        # Compat with Ubuntu 14.04 which ships libcap w/o .pc file
        libcap = cc.find_library('cap', required : false)
endif
conf.set10('HAVE_LIBCAP', libcap.found())

libcap_static = dependency('libcap', required : false, static : true)
if not libcap_static.found()
        # Compat with Ubuntu 14.04 which ships libcap w/o .pc file
        libcap_static = cc.find_library('cap', required : false, static : true)
endif
conf.set10('HAVE_STATIC_LIBCAP', libcap_static.found())

have = cc.has_function('strchrnul', prefix : '#include <string.h>', args : '-D_GNU_SOURCE')
conf.set10('HAVE_STRCHRNUL', have)

have = cc.has_function('openpty', prefix : '#include <pty.h>', args : '-D_GNU_SOURCE')
conf.set10('HAVE_OPENPTY', have)

foreach ccattr : ['fallthrough',
		  'nonnull',
		  'returns_nonnull',
		 ]

        conf.set10('HAVE_COMPILER_ATTR_' + ccattr.underscorify().to_upper(), cc.has_function_attribute(ccattr))
endforeach

config_h = configure_file(
        output : 'config.h',
        configuration : conf)

add_project_arguments('-include', 'config.h', language : 'c')

basic_includes = include_directories(
	'.',
	'src',
	'src/include')

liblxc_includes = [basic_includes, include_directories(
        'src/lxc/cgroups',
        'src/lxc/lsm',
        'src/lxc/storage')]

subdir('src/include')
subdir('src/lxc/tools/include')
subdir('src/lxc')

liblxc = shared_library(
        'lxc',
        version : liblxc_version,
	include_directories: liblxc_includes,
        link_args : ['-DPIC'],
        c_args : ['-DPIC'],
        link_whole : [liblxc_static],
        dependencies : [threads,
			libseccomp,
			libcap,
			libopenssl,
			libselinux,
			libapparmor],
        install : true)

liblxc_dep = declare_dependency(
		link_with: liblxc,
		dependencies : [threads,
				libseccomp,
				libcap,
				libopenssl,
				libselinux,
				libapparmor])

dummy_config_data = configuration_data()
dummy_config_data.set_quoted('DUMMY_VARIABLE', '1')

hook_programs = []
subdir('hooks')

template_scripts = []
subdir('templates')

cmd_programs = []
subdir('src/lxc/cmd')

public_programs = []
subdir('src/lxc/tools')

found_syscalls = []
missing_syscalls = []

foreach tuple : [
        ['bpf'],
        ['close_range'],
        ['endmntent'],
        ['execveat'],
        ['faccessat'],
        ['strchrnul'],
        ['fgetln'],
        ['fsconfig'],
        ['fsmount'],
        ['fsopen'],
        ['fspick'],
        ['getgrgid_r'],
        ['getline'],
        ['getsubopt'],
        ['gettid'],
        ['hasmntopt'],
        ['kcmp'],
        ['keyctl'],
        ['memfd_create'],
        ['mount_setattr'],
        ['move_mount'],
        ['openat2'],
        ['open_tree'],
        ['personality'],
        ['pidfd_open'],
        ['pidfd_send_signal'],
        ['pivot_root'],
        ['prlimit'],
        ['prlimit64'],
        ['renameat2'],
        ['sethostname'],
        ['setmntent'],
        ['setns'],
        ['signalfd'],
        ['statx'],
        ['strlcat'],
        ['strlcpy'],
        ['unshare'],
]

        if tuple.length() >= 2
                cond = tuple[1]
        else
                ident1 = 'HAVE_' + tuple[0].underscorify().to_upper()
                ident2 = 'ENABLE_' + tuple[0].underscorify().to_upper()
                cond = conf.get(ident1, 0) == 1 or conf.get(ident2, 0) == 1
        endif
        if cond
                found_syscalls += tuple[0]
        else
                missing_syscalls += tuple[0]
        endif
endforeach

found_types = []
missing_types = []

foreach tuple : [
        ['scmp_filter_ctx'],
        ['struct seccomp_notif_sizes'],
        ['struct clone_args'],
	['__aligned_u64'],
        ['struct mount_attr'],
        ['struct open_how'],
]

        if tuple.length() >= 2
                cond = tuple[1]
        else
                ident1 = 'HAVE_' + tuple[0].underscorify().to_upper()
                ident2 = 'ENABLE_' + tuple[0].underscorify().to_upper()
                cond = conf.get(ident1, 0) == 1 or conf.get(ident2, 0) == 1
        endif
        if cond
                found_types += tuple[0]
        else
                missing_types += tuple[0]
        endif
endforeach

found_headers = []
missing_headers = []

foreach tuple : [
	['sys/resource.h'],
        ['sys/memfd.h'],
        ['sys/personality.h'],
        ['sys/signalfd.h'],
        ['sys/timerfd.h'],
        ['pty.h'],
        ['utmpx.h' ],
]

        if tuple.length() >= 2
                cond = tuple[1]
        else
                ident1 = 'HAVE_' + tuple[0].underscorify().to_upper()
                ident2 = 'ENABLE_' + tuple[0].underscorify().to_upper()
                cond = conf.get(ident1, 0) == 1 or conf.get(ident2, 0) == 1
        endif
        if cond
                found_headers += tuple[0]
        else
                missing_headers += tuple[0]
        endif
endforeach

found_deps = []
missing_deps = []

foreach tuple : [
        ['AppArmor'],
        ['SECCOMP'],
        ['SELinux'],
        ['libcap'],
        ['openssl'],
]

        if tuple.length() >= 2
                cond = tuple[1]
        else
                ident1 = 'HAVE_' + tuple[0].underscorify().to_upper()
                ident2 = 'ENABLE_' + tuple[0].underscorify().to_upper()
                cond = conf.get(ident1, 0) == 1 or conf.get(ident2, 0) == 1
        endif
        if cond
                found_deps += tuple[0]
        else
                missing_deps += tuple[0]
        endif
endforeach

status = [
        '@0@ @1@'.format(meson.project_name(), meson.project_version()),

        'Meson version:			@0@'.format(meson.version()),

        'prefix directory:		@0@'.format(prefixdir),
        'bin directory:			@0@'.format(bindir),
        'data directory:		@0@'.format(datadir),
        'doc directory:			@0@'.format(docdir),
        'include directory:		@0@'.format(includedir),
        'lib directory:			@0@'.format(libdir),
        'libexec directory:		@0@'.format(libexecdir),
        'local state directory:		@0@'.format(localstatedir),
        'sbin directory:		@0@'.format(sbindir),
        'sysconf directory:		@0@'.format(sysconfdir),

        'lxc cgroup pattern:		@0@'.format(cgrouppattern),
        'lxc init directory:		@0@'.format(libexecdir),
        'runtime path:			@0@'.format(runtimepath),

        'lxc default config:		@0@'.format(lxcdefaultconfig),
        'lxc global config:		@0@'.format(lxcglobalconfig),
        'lxc hook directory:		@0@'.format(lxchookdir),
        'lxc hook bin directory:	@0@'.format(lxchookbindir),
        'lxc rootfs mount directory:	@0@'.format(lxcrootfsmount),
        'log path:			@0@'.format(lxclogpath),
        'lxc path:			@0@'.format(lxcpath),
        'lxc template config:		@0@'.format(lxctemplateconfig),
        'lxc template directory:	@0@'.format(lxctemplatedir),
        'lxc user network config:	@0@'.format(lxc_user_network_conf),
        'lxc user network database:	@0@'.format(lxc_user_network_db)]

alt_time_epoch = run_command('date', '-Is', '-u', '-d',
                             '@@0@'.format(time_epoch)).stdout().strip()
status += [
        'time epoch:			@0@ (@1@)'.format(time_epoch, alt_time_epoch)]

status += [
        '',
        'supported dependencies:	@0@'.format(', '.join(found_deps)),
        '',
        'unsupported dependencies:	@0@'.format(', '.join(missing_deps)),
        '']

status += [
        '',
        'supported headers:	@0@'.format(', '.join(found_headers)),
        '',
        'unsupported headers:	@0@'.format(', '.join(missing_headers)),
        '']

status += [
        '',
        'supported calls:	@0@'.format(', '.join(found_syscalls)),
        '',
        'unsupported calls:	@0@'.format(', '.join(missing_syscalls)),
        '']

status += [
        '',
        'supported types:	@0@'.format(', '.join(found_types)),
        '',
        'unsupported types:	@0@'.format(', '.join(missing_types)),
        '']

message('\n         '.join(status))
