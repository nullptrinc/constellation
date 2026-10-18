project('constellation', 'c',
  license: 'MIT',
  version: '0.1',
  default_options: ['c_std=gnu11', 'warning_level=2', 'buildtype=release'])

add_project_arguments('-Wno-unused-parameter', '-Wno-missing-field-initializers', language: 'c')

include = include_directories('include')

# The client library. Static for now: everything that talks to the broker
# links it in, so nothing extra has to ship at runtime.
constellation_lib = static_library('constellation', 'lib/object.c', 'lib/client.c',
  include_directories: include,
  pic: true,
  install: true)
constellation_dep = declare_dependency(link_with: constellation_lib,
  include_directories: include)
meson.override_dependency('constellation', constellation_dep)
install_headers('include/constellation.h')
import('pkgconfig').generate(constellation_lib,
  name: 'constellation',
  description: 'Client library for the Constellation /blinks broker')

executable('constellationd', 'daemon/constellationd.c',
  dependencies: constellation_dep,
  install: true)
executable('blinks', 'tools/blinks.c',
  dependencies: constellation_dep,
  install: true)

# System services: the only code that touches the hardware.
executable('constellation-power', 'services/power.c',
  dependencies: constellation_dep,
  install: true)

# sysvinit service for the broker and the services; link it into
# /etc/init.d as "constellation".
configure_file(input: 'data/constellation.init.in', output: 'constellation.init',
  configuration: {'bindir': get_option('prefix') / get_option('bindir')},
  install_dir: get_option('datadir') / 'constellation' / 'sysvinit', install_mode: 'rwxr-xr-x')
