import os

env = Environment()

mode = ARGUMENTS.get('mode', 'debug')
target = ARGUMENTS.get('target', 'compiler')

# Check build target
if target != 'compiler':
    print(f'Unsupported build target: [{target}]. Should be [compiler]')
    exit()
else:
    print(f'Building target: {target}.')

# Check build mode
if mode != 'debug' and mode != 'release':
    print(f'Unsupported compilation mode: [{mode}]. Should be either [release] or [debug]')
    exit(1)
else:
    print(f'Building project in {mode} mode.')

# Variables
COMPILER_FLAGS = []
BUILD_DIR = 'build'
system = env['PLATFORM']

print("Detected compiler:", os.environ.get("CC", "Not set"))

# Get compiler flags based on the OS
if system == 'Linux':
    print("Linux System Detected.")

    # Default flags for either build mode
    COMPILER_FLAGS.extend([
        '-Wall'
        , '-Wextra'
        , '-Werror'
        , '-Wpedantic'
    ])
    
    if mode == 'debug':
        print("Using compiler flags for Linux debug build.")
        COMPILER_FLAGS.extend([
            '-g'
            , '-DR_DEBUG'
            , '-fsanitize=address'
            , '-fsanitize=undefined'
            , '-fstack-protector-strong'
            , '-fno-omit-frame-pointer'
        ])
    elif mode == 'release':
        print("Using compiler flags for Linux release build.")
        COMPILER_FLAGS.extend([
            '-O3'
        ])
elif system == 'win32':
    print('Windows Systmem Detected')
    # Check for msvc compiler and use appropriate flags for debug and release
    # is_msvc = env['CXX'] == 'cl'
    is_msvc = 'msvc' in env['TOOLS']
    if is_msvc:
        print('MSVC compiler used')

        # Default flags for either mode
        COMPILER_FLAGS.extend([
            '/W4'
            , '/WX'
        ])

        if mode == 'debug':
            print('Using compiler flags for Windows:MSVC debug build.')

            COMPILER_FLAGS.extend([
                '/Od'       # Disable optimizations
                , '/Zi'       # Generate debug information
                , '/RTC1'     # Runtime error checks
                , '/MDd'      # Debug C runtime
                , '/EHsc'     # C++ exception handling (optional)
                , '/GS'       # Buffer security checks
                , '/DEBUG'    # Generate debug info for linker
                , '/DR_DEBUG' # Define _DEBUG macro
                , '/FC'       # Full path diagnostics
            ])
        elif mode == 'release':
            print('Using compiler flags for Windows:MSVC release build.')
            COMPILER_FLAGS.extend([
                '/O2'
                , '/GL'
                , 'Ot'
                , '/Oi'
                , '/Oy'
            ])
    else:
        if mode == 'debug':
            print(f'Using compiler flags for Windows:{env['CXX']} debug build.')
            COMPILER_FLAGS.extend([
                '-Wall'
                , '-Wextra'
                , '-Werror'
                , '-Wpedantic'
            ])
        elif mode == 'release':
            print(f'Using compiler flags for Windows:{env['CXX']} release build.')
            COMPILER_FLAGS.extend([
                '-O3'
            ])
elif system == 'Darwin':
    # NOTE: MacOS likely uses same compilers as Linux. Need to validate
    if mode == 'debug':
        COMPILER_FLAGS.extend([
            '-Wall'
            , '-Wextra'
            , '-Werror'
            , '-Wpedantic'
        ])
    elif mode == 'release':
        COMPILER_FLAGS.extend([
            '-O3'
        ])
else:
    print(f'Unsupported build platform: [{system}]')

for flag in COMPILER_FLAGS:
    print(f'FLAG: [{flag}]')

env.Append(CCFLAGS=COMPILER_FLAGS)

build_target = SConscript(f'{target}/SConscript', exports={'env': env, 'system': system}, variant_dir=f'{BUILD_DIR}/{target}', duplicate=0)