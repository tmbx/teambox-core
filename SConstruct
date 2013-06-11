#
# Copyright (C) 2006-2012 Opersys inc.
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

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
    BoolOption('mudflap', 'Build with mudflap (g 4.x)', 0),
    BoolOption('mpatrol', 'Build with mpatrol', 0),
    BoolOption('debug', 'Compile with all debug options turned on', 1),
    BoolOption('test', 'Build test', 1),
    ('PREFIX', 'Base directory to install the lib', '/usr/local'),
    ('BINDIR', 'Directory to install binary files', '$PREFIX/bin'),
    ('LIBDIR', 'Directory to install library files', '$PREFIX/lib'),
    ('PYTHONDIR', 'Directory to install Python files', '$PREFIX/share/teambox/python'),
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
SConscript('kpython/SConscript', duplicate = 0, exports = 'env')
SConscript('kweb/SConscript',duplicate = 0, exports = 'env')

# install
install.append(env.Dir(name=env['BINDIR']))
install.append(env.Dir(name=env['LIBDIR']))
install.append(env.Dir(name=env['INCDIR']))
install.append(env.Dir(name=env['PYTHONDIR']))
env.Alias('install', install)

# clean
if 'clean' in COMMAND_LINE_TARGETS:
    SetOption("clean", 1)
    Alias("clean", builds)
