project('libcheck')

cdata = configuration_data()
cdata.set('ENABLE_SUBUNIT', 0)
cdata.set('CHECK_MAJOR_VERSION', 0)
cdata.set('CHECK_MINOR_VERSION', 12)
cdata.set('CHECK_MICRO_VERSION', 0)

if host_machine.system() != 'windows'
  cdata.set('HAVE_FORK', 1)
else
  # libcheck requires HAVE_FORK to be 0 when fork() is not available
  cdata.set('HAVE_FORK', 0)
endif

cc = meson.get_compiler('c')
# Must explicitly make symbols public if default visibility is hidden
have_visibility_hidden = cc.has_argument('-fvisibility=hidden')
if have_visibility_hidden
  libcheck_visibility_args = ['-DCK_DLL_EXP=extern __attribute__ ((visibility ("default")))']
else
  if host_system == 'windows'
    libcheck_visibility_args = ['-DCK_DLL_EXP=__declspec(dllexport)']
  else
    libcheck_visibility_args = ['-DCK_DLL_EXP=extern']
  endif
endif

no_warn_args = []
foreach arg : [
  '-Wno-undef',
  '-Wno-redundant-decls',
  '-Wno-missing-prototypes',
  '-Wno-missing-declarations',
  '-Wno-old-style-definition',
  '-Wno-declaration-after-statement',
  '-Wno-unused-parameters',
  ]
  if cc.has_argument(arg)
    no_warn_args += [arg]
  endif
endforeach

sources = []
include_directories = []
subdir('lib')
subdir('src')

libcheck = static_library('check',
  sources,
  include_directories : include_directories,
  c_args: libcheck_visibility_args + no_warn_args +
	  # Don't want libcompat to think we don't have these and substitute
	  # replacements since we don't check for or define these. See libcompat.h
	  ['-DHAVE_VSNPRINTF', '-DHAVE_SNPRINTF', '-DHAVE_MALLOC',
       '-DHAVE_REALLOC'],
  pic: true)
