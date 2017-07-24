project('libelf','c',version:'0.8.13')
add_project_arguments('-DHAVE_CONFIG_H',language:'c')
c_compiler = meson.get_compiler('c')

libelf_version = meson.project_version()
libelf_version_arr = libelf_version.split('.')

conf = configuration_data()

if not c_compiler.has_type('off_t',prefix:'#include <sys/types.h>')
    conf.set('off_t','long')
endif

if not c_compiler.has_type('size_t',prefix:'#include <sys/types.h>')
    conf.set('size_t','unsigned')
endif


STDC_HEADERS=[
  'assert.h', 'limits.h', 'signal.h', 'stdlib.h',
  'ctype.h', 'locale.h', 'stdarg.h', 'string.h',
  'errno.h', 'math.h', 'stddef.h', 'time.h',
  'float.h', 'setjmp.h', 'stdio.h', 'iso646.h',
  'wchar.h', 'wctype.h', 'complex.h', 'inttypes.h',
  'stdint.h', 'tgmath.h', 'fenv.h', 'stdbool.h']

has_std_headers = true
foreach header:STDC_HEADERS
  if not c_compiler.has_header(header)
    has_std_headers = false
  endif
endforeach

if has_std_headers
  conf.set('STDC_HEADERS',1)
endif

conf.set('ENABLE_EXTENDED_FORMAT',1)
conf.set('ENABLE_SANITY_CHECKS',1)

functions = [
 ['mmap', '#include <sys/mman.h>', 'HAVE_MMAP'],
 ['memccpy', '#include <string.h>', 'HAVE_DECL_MEMPCPY'],
 ['catgets', '#include <nl_types.h>','HAVE_CATGETS'],
 ['dgettext', '#include <libintl.h>', 'HAVE_DGETTEXT'],
 ['memset', '#include <string.h>', 'HAVE_MEMSET']
]

foreach function:functions
  if c_compiler.has_function(function[0], prefix : function[1])
    conf.set(function[2],1)
  endif
endforeach

types = [
  ['struct nlist', '#include <elf.h>', 'HAVE_STRUCT_NLIST_DECLARATION'],
  ['struct nlist', '#include <sys/elf.h>', 'HAVE_STRUCT_NLIST_DECLARATION'],
  ['Elf32_Dyn', '#include <link.h>', '__LIBELF_NEED_LINK_H'],
  ['Elf32_Dyn', '#include <sys/link.h>', '__LIBELF_NEED_SYS_LINK_H']
]

foreach type:types
  if c_compiler.has_type(type[0], prefix : type[1])
    conf.set(type[2],1)
  endif
endforeach

if c_compiler.has_header('sys/elf.h')
  conf.set('__LIBELF_HEADER_ELF_H','<sys/elf.h>')
endif
if c_compiler.has_header('elf.h')
  conf.set('__LIBELF_HEADER_ELF_H','<elf.h>')
endif

if 8 == c_compiler.sizeof('long')
  conf.set('__libelf_i64_t', 'long')
  conf.set('__libelf_u64_t', 'unsigned long')
  conf.set('__LIBELF64', 1)
elif 8 == c_compiler.sizeof('long long')
  conf.set('__libelf_i64_t', 'long long')
  conf.set('__libelf_u64_t', 'unsigned long long')
  conf.set('__LIBELF64', 1)
endif

if 4 == c_compiler.sizeof('int')
  conf.set('__libelf_i32_t', 'int')
  conf.set('__libelf_u32_t', 'unsigned int')
elif 4 == c_compiler.sizeof('long')
  conf.set('__libelf_i32_t', 'long')
  conf.set('__libelf_u32_t', 'unsigned long')
endif

if 2 == c_compiler.sizeof('short')
  conf.set('__libelf_i16_t', 'short')
  conf.set('__libelf_u16_t', 'unsigned short')
elif 2 == c_compiler.sizeof('int')
  conf.set('__libelf_i16_t', 'int')
  conf.set('__libelf_u16_t', 'unsigned int')
endif

conf.set_quoted('VERSION', libelf_version)

message(libelf_version)
config_h = configure_file(input : 'config.h.in.meson',
               output : 'config.h',
               configuration : conf)

sys_elf_h = configure_file(input : 'lib/sys_elf.h.in.meson',
               output : 'sys_elf.h',
               configuration : conf)


libelf_incdir = include_directories('.', './lib')

zlib = dependency('zlib')

libelf_sources=[
'lib/32.fsize.c',
'lib/32.getehdr.c',
'lib/32.getphdr.c',
'lib/32.getshdr.c',
'lib/32.newehdr.c',
'lib/32.newphdr.c',
'lib/32.xlatetof.c',
'lib/64.xlatetof.c',
'lib/assert.c',
'lib/begin.c',
'lib/checksum.c',
'lib/cntl.c',
'lib/cook.c',
'lib/data.c',
'lib/end.c',
'lib/errmsg.c',
'lib/errno.c',
'lib/fill.c',
'lib/flag.c',
'lib/gelfehdr.c',
'lib/gelfphdr.c',
'lib/gelfshdr.c',
'lib/gelftrans.c',
'lib/getarhdr.c',
'lib/getaroff.c',
'lib/getarsym.c',
'lib/getbase.c',
'lib/getdata.c',
'lib/getident.c',
'lib/getscn.c',
'lib/hash.c',
'lib/input.c',
'lib/kind.c',
'lib/memset.c',
'lib/ndxscn.c',
'lib/newdata.c',
'lib/newscn.c',
'lib/next.c',
'lib/nextscn.c',
'lib/nlist.c',
'lib/opt.delscn.c',
'lib/rand.c',
'lib/rawdata.c',
'lib/rawfile.c',
'lib/strptr.c',
'lib/swap64.c',
'lib/update.c',
'lib/verdef_32_tof.c',
'lib/verdef_32_tom.c',
'lib/verdef_64_tof.c',
'lib/verdef_64_tom.c',
'lib/version.c',
'lib/x.elfext.c',
'lib/x.movscn.c',
'lib/x.remscn.c'
]

mkdir = find_program('mkdir')
cp = find_program('cp')

libelf_folder = custom_target('libelf_folder',
  output : 'libelf_folder',
  command : [mkdir,'-p' ,meson.current_build_dir() + '/libelf']
  )

libelf_sys_cp = custom_target('libelf_sys_cp',
  output : 'libelf_sys_cp',
  command : [cp, meson.current_build_dir() + '/sys_elf.h' , meson.current_build_dir() + '/libelf/'],
  depends : libelf_folder
  )

libelf_cp = custom_target('libelf_cp',
  output : 'libelf_cp',
  command : [cp, meson.current_source_dir() + '/lib/libelf.h' , meson.current_build_dir() + '/libelf/'],
  depends : libelf_folder
  )

libelf_lib = library('libelf', libelf_sources,
  include_directories : [libelf_incdir],
  dependencies:[zlib],
  link_depends:[libelf_cp,libelf_sys_cp]
)


libelf_dep = declare_dependency(link_with : libelf_lib,
                                include_directories : libelf_incdir)

