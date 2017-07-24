data = configuration_data()
configure_file(
	input:'../lib/libelf.h',
	output:'libelf.h',
	configuration: data
)

configure_file(input : '../lib/sys_elf.h.in.meson',
               output : 'sys_elf.h',
               configuration : conf)
