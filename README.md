bash-spec
=========

This is a modified version of bash-spec from https://github.com/neopragma/bash-spec. The most important changes are:

- The "describe" and "context" syntaxes are available
- The "it" syntax has changed
- All the above syntaxes are nestable and work via command substitution (i.e. `"$( commands here )"` )
- A few bugs around failure messages have been fixed (with their own tests, of course)

# Usage

## Installation

Just put the bash-spec.sh and bash-spec-ext.sh files in your project in a folder of your choice.

## Writing tests

Your test file (a bash script you'll write) needs to source bash-spec-ext.sh:

    . ./path-to-files/bash-spec-ext.sh

Then you can describe a thing and how you expect it to behave:

```
describe "The word Penguin" "$( 
  
  word="Penguin"
  
  it "Is seven letters long" "$(
    expect "${#word}" to_be "7"
  )"
  
  it "Begins with the capital letter P" "$(
    expect "${word:0:1}" to_be "P"
  )"
  
  context "When converted to lowercase" "$(
  
    word=$( echo $word | tr '[:upper:]' '[:lower:]' )
    
    it "Begins with the lower case letter p" "$(
      expect "${word:0:1}" to_be "p"
    )"
    
  )"
  
)"
```

OK, so we're mixing tests with code under test a bit here, but it's a standalone example of the syntax, so deal.

Running the test script (don't forget to make it executable) will give you the following:

```
The word Penguin
  Is seven letters long
         PASS
  Begins with the capital letter P
         PASS
When converted to lowercase
  Begins with the lower case letter p
         PASS
```

### Matchers

Most of the matchers from the original bash-spec survived. The to_be_installed matcher went away, because it didn't work and wasn't super useful. It can be resurrected if someone cares enough.

The available matchers are:

- `to_be` (equality)
- `to_match` (regex matching)
- `to_contain` (array element equality)
- `to_exist` (file existence)
- `to_have_mode` (file permissions matching)
- `to_be_true` (exit mode matcher)

Each matcher has a negated mode (`not to_be`, `not to_match` etc)

### Blocks and the notably absent "before" syntax

You'll have noticed that the command substitution syntax is used. This provides something similar to independent blocks, since each "$( )" spawns a subshell that doesn't affect other subshells or the parent shell. Each subshell also gets a copy of the environment in the parent shell, making a "before" syntax unnecessary.

The [bash-spec test suite](https://github.com/realestate-com-au/bash-spec-2/blob/master/test_bash-spec.sh) has some good examples of this.

### Mocks

bash-spec-2 works hand in hand with [stub.sh](https://github.com/jimeh/stub.sh) to allow for mocking. You can do things like calling AWS via chains of your own functions to build up the command line and test it with a stubbed "aws." See the stub.sh docs for details or see examples in [the wiki](https://github.com/realestate-com-au/bash-spec-2/wiki).

# TODO list

- An equivalent for let blocks (memoized, lazy evaluated) might be useful
- Proper nesting of the output would be cool
