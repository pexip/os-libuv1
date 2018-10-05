# TODO
# - double check min. version needed for meson
project ('uv', 'c', version : '2.9.1',
          meson_version: '>= 0.33.0')

host_system = host_machine.system()
cc = meson.get_compiler('c')

project_name = meson.project_name()
project_arguments = []
project_includes = []
project_dependencies = []
project_includes += [ include_directories ('include', 'src') ]

headers = [
    'include/uv-errno.h',
    'include/uv-threadpool.h',
    'include/uv-version.h',
    'include/uv.h',
]

sources = [
    'src/fs-poll.c',
    'src/inet.c',
    'src/uv-common.c',
    'src/version.c',
    'src/threadpool.c',
]

if host_system == 'windows'
    project_arguments += [
        '/DWIN32_LEAN_AND_MEAN',
        '/D_WIN32_WINNT=0x0600',
        '/D_GNU_SOURCE',
        '/DBUILDING_UV_SHARED=1',   # enable building shared library (dll)
    ]

    project_includes += [ include_directories('src/win') ]

    win_libs = [
        'advapi32',
        'shell32',
        'user32',
        'userenv',
        'psapi',
        'iphlpapi',
        'ws2_32'
    ]
    foreach d : win_libs
        project_dependencies += cc.find_library(d)
    endforeach

    headers += [
        'include/uv-win.h',
        'include/tree.h',
    ]

    sources += [
        'src/win/winapi.c',
        'src/win/winsock.c',
        'src/win/async.c',
        'src/win/core.c',
        'src/win/dl.c',
        'src/win/error.c',
        'src/win/fs-event.c',
        'src/win/fs.c',
        'src/win/getaddrinfo.c',
        'src/win/getnameinfo.c',
        'src/win/handle.c',
        'src/win/loop-watcher.c',
        'src/win/pipe.c',
        'src/win/poll.c',
        'src/win/process-stdio.c',
        'src/win/process.c',
        'src/win/req.c',
        'src/win/signal.c',
        'src/win/stream.c',
        'src/win/tcp.c',
        'src/win/thread.c',
        'src/win/timer.c',
        'src/win/tty.c',
        'src/win/udp.c',
        'src/win/util.c',
    ]
elif host_system == 'linux'
    project_arguments += [
        '-fPIC',
        '-fvisibility=hidden',
        '-g',
        '--std=gnu89',
        '-pedantic',
        '-Wall',
        '-Wextra',
        '-Wno-unused-parameter',
        '-Wstrict-aliasing',

        '-D_POSIX_C_SOURCE=200112',
        '-D_LARGEFILE_SOURCE',
        '-D_FILE_OFFSET_BITS=64',
        '-D_GNU_SOURCE',
        '-DBUILDING_UV_SHARED=1',
    ]

    project_includes += [ include_directories('src/unix') ]

    project_dependencies += dependency('threads')

    unix_libs = [
        'm',
        'dl',
        'rt',
    ]
    foreach d : unix_libs
        project_dependencies += cc.find_library(d)
    endforeach

    headers += [
        'include/uv-unix.h',
        'include/uv-linux.h',
        # 'include/uv-sunos.h',
        # 'include/uv-darwin.h',
        # 'include/uv-bsd.h',
        # 'include/uv-aix.h',
  ]

  sources += [
      'src/unix/async.c',
      'src/unix/atomic-ops.h',
      'src/unix/core.c',
      'src/unix/dl.c',
      'src/unix/fs.c',
      'src/unix/getaddrinfo.c',
      'src/unix/getnameinfo.c',
      'src/unix/internal.h',
      'src/unix/loop.c',
      'src/unix/loop-watcher.c',
      'src/unix/pipe.c',
      'src/unix/poll.c',
      'src/unix/process.c',
      'src/unix/signal.c',
      'src/unix/spinlock.h',
      'src/unix/stream.c',
      'src/unix/tcp.c',
      'src/unix/thread.c',
      'src/unix/timer.c',
      'src/unix/tty.c',
      'src/unix/udp.c',
      'src/unix/linux-core.c',
      'src/unix/linux-inotify.c',
      'src/unix/linux-syscalls.c',
      'src/unix/linux-syscalls.h',
  ]
endif

add_project_arguments (project_arguments, language : 'c')

pkgconf = configuration_data()
pkgconf.set('prefix', join_paths(get_option('prefix')))
pkgconf.set('exec_prefix', '${prefix}')
pkgconf.set('libdir', '${prefix}/@0@'.format(get_option('libdir')))
pkgconf.set('includedir', '${prefix}/@0@'.format(get_option('includedir')))
pkgconf.set('PACKAGE', project_name)
pkgconf.set('PACKAGE_NAME', project_name)
pkgconf.set('PACKAGE_VERSION', meson.project_version())
# TODO: @LIBS@ in libuv.pc.in?
pkg_install_dir = join_paths(get_option('prefix'), get_option('libdir'), 'pkgconfig')
configure_file(input : 'libuv.pc.in',
  output : 'libuv.pc',
  configuration : pkgconf,
  install_dir : pkg_install_dir)

install_headers (headers)

shared_library (project_name,
    sources,
    include_directories : project_includes,
    dependencies : project_dependencies,
    install : true,
)
