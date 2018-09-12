Bit
===
Bit is an [esoteric programming language](https://esolangs.org/wiki/Esoteric_programming_language) created by me, read the specification for details, and feel free to write some programs with it using the interpreter in this repository (`java -jar bit.jar filename.bit`)

Bit Specification
=================
First off, there are two built in stacks: the *bitstack* and the *stack*.
Second, throughout this specification, the letter 'b' will be used to indicate a value in binary, and `[x]` will indicate that x is an optional argument. An *overriding operation* is one that sets a variable, overriding it if it already exists. (most operations are overriding unless explicitly stated, for any questions feel free to open an issue)

- `$$` - A comment
- `BIT 0` - Pushes a 0 to the *bottom* of the bitstack
- `BIT 1` - Pushes a 1 to the *bottom* of the bitstack
- `BYTE` - Converts the bitstack into a byte (bitstack: 1, 0 = 10b or 2), clears it, then pushes the byte to the top of the normal stack
- `BYTE varname` - Converts the bitstack into a byte, clears it, then sets the specified variable to that byte (overriding operation)
- `BYTES n [var]` Iterates through the bitstack, splitting it every n bits. Then, it takes each one of the split parts and converts them to a byte, then stores the bytes in the stack *as a single element* or in `var` if specified (bitstack: 1,0,1,0,1,0,1,0 = [1010b, 1010b] if n = 4)
- `ADD` - Pops 2 values from the stack, adds them, and pushes the result to the stack (Errors if a non-number value is popped, like that of calling `BYTES` or `STORE`)
- `ADD a` - Pops a value from the stack, adds it to `a`, and pushes the result to the stack
- `ADD a b` - Adds a and b, and pushes the result to the stack
- `ADD a b var` - Adds a and b, and stores the result in `var` (if `var` is an array created by `BYTES` or `STORE`, it pushes the result to `
var` instead of setting it)
- `SUBTRACT [a] [b] [var]` - Same as above 3, but with subtraction.
- `MULTIPLY [a] [b] [var]` - Same as above 3, but with multiplication.
- `LOG a` - Pops b, pushes log a of b
- `LOG a b` - Pushes log a of b
- `LOG a b var` - Stores log a of b in `var` (If `var` us an array, push instead of setting)
- `POWER [a] [b] [var]` - Same as above 3, but with exponents.
- `TRUNC` - Pops a and b, pushes `a` truncated to the `b`th decimal
- `TRUNC a` - Pops b, pushes `b` truncated to the `a`th decimal
- `TRUNC a b` - Pushes `a` truncated to the `b`th decimal
- `TRUNC a b var` - Stores `a` truncated to the `b`th decimal in `var` (If `var` us an array, push instead of setting)
- `DUMP_ARRAY a [b]` - If b is specified, add all contents of b to a, otherwise add all contents of a to the stack
- `DUMP_STACK` - Clears the stack
- `DUMP_STACK a` - For an array a, override it with the stack (unless the stack is 1 element long in which a is overridden with that element) and clear the stack, for a non-array a, override it with a popped value
- `DUMP var` - Push var to the stack
- `DUP` - Push the contents of the stack to the stack itself
- `DUP var` - Push the contents of `var` to `var`
- `FLIP [var]` - Flip the contents of `var` or the stack
- `IMPORT var` - Runs the code inside the file with path `var` (stringified) as if the `IMPORT` statement were replaced by the code inside the file (aka: it isn't sandboxes). If the file doesn't exist, the statement silently exits and does nothing
- `IN [prompt] [var]` - If `prompt` is not specified, prompt = "" (empty), then stringify prompt (see below) and print it with no newline, then read from STDIN. Take each character's ASCII value and push it to the stack *as a single element* OR override `var` with that list of values. Example: `IN somevar othervar`, othervar = [65, 65, 67] if user typed ABC
- `INTO` - Pops a value from the stack, and sets the stack to that value if it is an array, otherwise throw an error.
- `OUTOF` - Set stack to a new stack containing the old stack, `stack = [stack]`
- `POP [n]` - If n is not specified, pop a value from the stack, otherwise pop n values from the stack
- `PRINT [var]` - If `var` is not specified, add a popped value to the *printing queue*, otherwise if `var` is an array, add all elements of it to the printing queue, otherwise if `var` is a number, add it to the printing queue
- `PRINTLN` - Stringify the printing queue, then print it to STDOUT with a trailing newline, then clear the printing queue
- `PUSH var` - Pop a value from the stack, and override `var` with it
- `SHIFT` - Rotates the stack by `amount` or 1 (stack: [1, 2, 3] -> [3, 1, 2])
- `SHIFT var` - Same as above but with var
- `STORE` - Behaves exactly like `BYTES`, but has a fixed amount of elements, so elements pushed to it override others ([0, 1,2,3] PUSH 4 = [1,2,3,4] removing 0), and if any are popped/missing, they are replaced with zeroes (This even works with `IN`, if the user doesn't type enough characters, zeroes will be inserted at the start of the stack)

## Stringification
Stringification is the process of turning an array (created by `BYTES` or `STORE`) into a string, to do this, iterate over the array, and convert each ASCII value to a character, concatenate all the characters and that is the result of the stringification

## Invalid values
For example, `BYTES 0` or dividing by 0, the interpreter will stop with an error (a bug makes it so it doesn't stop on `BYTES 0`, and I currently can't fix it)

Examples
========
Program that prints 'A':
```
BIT 1 $$ Add the bits of 65
BIT 0
BIT 0
BIT 0
BIT 0
BIT 0
BIT 1 $$ Most significant bits are here!

BYTE
PRINT
PRINTLN
```
Cat program:
```
IN $$ Takes input and pushes it to the stack
PRINT $$ Pops an item, which is an array of character codes in this case, and puts the characters in the printing queue
PRINTLN $$ Print the printing queue
```
