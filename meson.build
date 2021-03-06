# https://mesonbuild.com/Builtin-options.html

project(
	'wot++',
	'cpp',
	license: 'MPL-2.0',
	version: '1.0',
	default_options: ['cpp_std=c++17', 'buildtype=release', 'strip=true', 'warning_level=2']
)

extra_opts = []

modules_inc = include_directories('modules/')

sources = files('src/main.cpp')
sources_inc = include_directories('src/')


# Set error limit.
if meson.get_compiler('cpp').get_id() == 'clang'
	add_project_arguments('-ferror-limit=2', language: 'cpp')

elif meson.get_compiler('cpp').get_id() == 'gcc'
	add_project_arguments('-fmax-errors=2', language: 'cpp')
endif


# Profiling support.
if get_option('profile')
	if meson.get_compiler('cpp').has_argument('-finstrument-functions')
		add_project_arguments('-finstrument-functions', language: 'cpp')

	else
		error('profiling is unsupported')
	endif
endif


# Sanitizer support.
if get_option('sanitizers')
	if meson.get_compiler('cpp').has_argument('-fsanitize=address,undefined')
		extra_opts += ['b_sanitize=address,undefined']

	else
		error('sanitizers are not unsupported')
	endif
endif


if get_option('disable_run')
	add_project_arguments('-DWPP_DISABLE_RUN', language: 'cpp')
endif


libasan_dep = meson.get_compiler('cpp').find_library('asan', required: get_option('sanitizers'))
libstdcppfs_dep = meson.get_compiler('cpp').find_library('stdc++fs', required: false)


exe = executable(
	'w++',
	sources,
	include_directories: [sources_inc, modules_inc],
	dependencies: [libstdcppfs_dep, libasan_dep],
	install: true,
	native: get_option('native'),
	override_options: extra_opts
)


# Test cases
test_runner = find_program('tests/run_test.py')

test_cases = [
	'tests/func.wpp',
	'tests/eval.wpp',
	'tests/nested_func.wpp',
	'tests/comments.wpp',
	'tests/blocks.wpp',
	'tests/strings.wpp',
]

if not get_option('disable_run')
    test_cases += ['tests/run.wpp']
endif

foreach case: test_cases
	test(case, test_runner, args: [files(case), exe])
endforeach


