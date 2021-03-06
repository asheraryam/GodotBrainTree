#!python
import os

# platform= makes it in line with Godots scons file, keeping p for backwards compatibility
platform = ARGUMENTS.get("p", "linux")
platform = ARGUMENTS.get("platform", platform)

# This makes sure to keep the session environment variables on windows, 
# that way you can run scons in a vs 2017 prompt and it will find all the required tools
env = Environment()
if platform == "windows":
    env = Environment(ENV = os.environ)

godot_headers_path = ARGUMENTS.get("headers", os.getenv("GODOT_HEADERS", "godot_headers"))
godot_bindings_path = ARGUMENTS.get("cpp_bindings", os.getenv("CPP_BINDINGS", "cpp_bindings"))

# default to debug build, must be same setting as used for cpp_bindings
target = ARGUMENTS.get("target", "debug")


if ARGUMENTS.get("use_llvm", "no") == "yes":
    env["CXX"] = "clang++"

# put stuff that is the same for all first, saves duplication 
if platform == "osx":
    env.Append(CCFLAGS = ['-g0','-O2', '-std=c++14', '-arch', 'x86_64'])
    env.Append(LINKFLAGS = ['-arch', 'x86_64', '-framework', 'Cocoa', '-Wl,-undefined,dynamic_lookup'])
elif platform == "linux": ##### LINUX
    env.Append(CCFLAGS = ['-g0','-O3', '-std=c++14', '-Wno-writable-strings'])
    env.Append(LINKFLAGS = ['-Wl,-R,\'$$ORIGIN\''])
elif platform == "windows": #### WINDOWS
    # need to add detection of msvc vs mingw, this is for msvc...
    env.Append(LINKFLAGS = ['/WX'])
    if target == "debug":
        env.Append(CCFLAGS = ['-EHsc', '-D_DEBUG', '/MDd'])
    else:
        env.Append(CCFLAGS = ['-O2', '-EHsc', '-DNDEBUG', '/MD'])

def add_sources(sources, dir):
    for f in os.listdir(dir):
        if f.endswith(".cpp"):
            sources.append(dir + "/" + f)

env.Append(CPPPATH=[godot_headers_path, godot_bindings_path + '/include/', godot_bindings_path + '/include/core/'])

env.Append(LIBS=['godot-cpp.linux.64'])
env.Append(LIBPATH=[ godot_bindings_path + '/bin/' ])

sources = []
add_sources(sources, "src")

library = env.SharedLibrary(target='addons/kakoeimon.braintree/lib/libbraintree', source=sources)
Default(library)

