#
# SConstruct
# Copyright (C) 2005-2012 Opersys inc., All rights reserved.
#

import commands, os, sys, re
from kenv import KEnvironment

EnsurePythonVersion(2,3)
VariantDir('build', 'libktools')

# Custom test to get all libraries needed to link with libgcrypt
def CheckGCrypt(context):
    context.Message("Checking for libgcrypt-config...")
    if commands.getstatusoutput('which libgcrypt-config')[0] == 0:
        env['LINKFLAGS'] += commands.getoutput('libgcrypt-config --libs').strip().split()    
        context.Result('ok')
        return 1
    else:
        context.Result('failed')
        return 0

lib_OPTIONS = Options('build.conf')
lib_OPTIONS.AddOptions(
    BoolOption('mudflap', 'Build with mudflap (gcc 4.x)', 0),
    BoolOption('mpatrol', 'Build with mpatrol', 0),
    BoolOption('debug', 'Compile with all debug options turned on', 1),
    BoolOption('test', 'Build test', 1),
    ('PREFIX', 'Base directory to install the lib', '/usr/local'),
    ('BINDIR', 'Directory to install binary files', '$PREFIX/bin'),
    ('LIBDIR', 'Directory to install library files', '$PREFIX/lib'),
    ('PYTHONDIR', 'Directory to install Python files', '$PREFIX/share/python'),
    ('INCDIR', 'Directory where to install the header files', '$PREFIX/include'),
    ('WIN_PTHREAD', 'Path to the pthread library on Windows', 'C:/birtz/lib/pthreads-w32-2-8-0-release'))

# Setup the build environment.
env = KEnvironment(options = lib_OPTIONS)

lib_OPTIONS.Save('build.conf', env)

# Generate the help text.
Help(lib_OPTIONS.GenerateHelpText(env))

# build the lib
builds, install = SConscript('libktools/SConscript', 
                             duplicate = 0, 
                             exports = 'env')
builds, install = SConscript('tagcrypt/SConscript',
                             duplicate = 0,
                             exports = 'env',
                             variant_dir = os.path.join('build', 'tagcrypt'))
builds, install = SConscript('kpython/SConscript',
                             duplicate = 0,
                             exports = 'env')

# install
install.append(env.Dir(name=env['LIBDIR']))
install.append(env.Dir(name=env['INCDIR']))
install.append(env.Dir(name=env['PYTHONDIR']))
env.Alias('install', install)

# clean
if 'clean' in COMMAND_LINE_TARGETS:
    SetOption("clean", 1)
    Alias("clean", builds)
