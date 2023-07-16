# configng
This is a refactoring of [armbian-config](https://github.com/armbian/config) using [Bash Utility](https://labbots.github.io/bash-utility) 
embedded in this project. This allows for functional programming in Bash. Error handling and validation are also included. 
The idea is to provide an API in Bash that can be called from a Command line interface, Text User interface and others.
Why Bash? Well, because it's going to be in every distribution. Striped down distributions 
may not include Python, C/C++, etc. build/runtime environments 

## Quick start
* `sudo apt install git`
* `cd ~/`
* `git clone https://github.com/armbian/configng.git`
* `sudo ~/configng/configng.sh`
  
#### If all goes well you should see list or avalible commands 
```
Usage: configng [ -h | foo ]

Options:
 -h)  Print this help.

 foo)  Usage: configng foo [ boardled::options ][ cpu::options ][ extra_drive::options ][ benchymark::options ]::

        boardled::options
                set_sysled_cpu  set the Sys board led to montor cpu activity.
                set_sysled_none set the Sys board led to montor none.
                see_sysled      See a list of board led options.

        cpu::options
                see_policy      Return policy as int based on original armbian-config logic.
                see_freqs       Return CPU frequencies as string delimited by space.
                see_min_freq    Return CPU minimum frequency as string.
                see_max_freq    Return CPU maximum frequency as string.
                see_governor    Return CPU governor as string.
                see_governors   Return CPU governors as string delimited by space.
                set_freq        Set min, max and CPU governor.

        extra_drive::options
                set_spi_vflash  Set up a simulated MTD spi flash for testing.
                rem_spi_vflash  Remove tsting simulated MTD spi flash.

        benchymark::options
                see_monitor     system boot-up performance statistics.
                see_boot_blame  system boot-up performance statistics.
                see_7ZipBench   7-zip benchmark based on original armbianmonitor logic.

```

## Coding standards
[Shell Style Guide](https://google.github.io/styleguide/shellguide.html) has some good ideas, 
but fundementally look at the code in Bash Utility:
```
# @description Strip characters from the beginning of a string.
#
# @example
#   echo "$(string::lstrip "Hello World!" "He")"
#   #Output
#   llo World!
#
# @arg $1 string The input string.
# @arg $2 string The characters you want to strip.
#
# @exitcode 0  If successful.
# @exitcode 2 Function missing arguments.
#
# @stdout Returns the modified string.
string::lstrip() {
[[ $# -lt 2 ]] && printf "%s: Missing arguments\n" "${FUNCNAME[0]}" && return 2
printf '%s\n' "${1##$2}"
}
```

Functions should follow ~~filename~~::func_name style. Then you can tell just from the name which 
file the function is located in. Return codes should also follow a similar pattern:
* 0 Successful
* 1 Not found
* 2 Function missing arguments
* 3-255 all other errors

Validate values:
```
# Validate minimum frequency is <= maximum frequency
[ "$min_freq" -gt "$max_freq" ] && printf "%s: Minimum frequency must be <= maximum frequency\n" "${FUNCNAME[0]}" && return 5
```

Return values should use stdout:
```
# Return value
printf '%s\n' "$(cat $file)"
```

Only use sudo when needed and never run as root!
