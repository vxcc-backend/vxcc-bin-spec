## linker VM

## memory model
all objects have to be either reference counted or garbage collected by the implementation.

## exceptions
when any error occurs, like array index out of bounds, the VM has abort immediately.

## datatypes
- ptr: a integer of at least the minimum size required to store symbol adresses.
- int: a 32 bit integer
- array: stores a sequence of elements that can all be different datatypes.

## registers
there are 256 registers, but register 0 stores a hardwired 0 number.
all registers can store any datatype.

when more storage for temporary values is needed, arrays can be used.

## instruction encoding
- register-register computation:   `00cccccc [dst_reg:8b] [src0_reg:8b] [src1_reg:8b]`
- register-immediate computation:  `01cccccc [dst_reg:8b] [src0_reg:8b] [imm8_sext:8b]` (immediate is sign extended to datatype of second arg)
- relative jump:                   `1000ixxx [test_reg:8b] [relative:16b]` (relative is sign extended)

where:
- `c` is the opcode
- `x` is the condition
- `i` inverts the condition

## conditions
| `xxx` | pseudocode |
| ----- | ---------- |
| `000` | `v == 0`   |
| `001` | `v == 1`   |
| `010` | `v == 2`   |
| `011` | `v == 3`   |
| `100` | `arr_length(v) == 0` |
| `101` | `arr_get(v, 0) == 0` |
| `110` | `v < 0`    |
| `111` | `true`     |

## pseudocode for jump
```
if (i ^ check_cond(xxx, v)) {
  pc += sign_extend(relative);
}
```

## opcodes
|| `cccccc` | name   | out | arg0 | arg1 | description |
|-| -------- | ------ | --- | ---- | ---- | ----------- |
|movement| `000000` | `mov`  | `T`  |  `T`  | -  | move between registers |
|| `000001` | `movi_ptr`  | `T`  |  - | `ptr`  | move sign extended immediate as ptr into register |
|| `000010` | `movi_int`  | `T`  |  - | `ptr`  | move sign extended immediate as int into register |
|| `000011` | - | -  |  - | -  | reserved |
|| `000100` | `convt_int`  | `int`  | `ptr/int` | - | truncating convert ptr or int to int |
|| `000101` | `convt_ptr`  | `ptr`  | `ptr/int` | - | truncating convert ptr or int to ptr |
|| `000110` | `convs_int`  | `int`  | `ptr/int` | - | sign extending convert ptr or int to int. (identical to `convt_int`) |
|| `000111` | `convs_ptr`  | `ptr`  | `ptr/int` | - | sign extending convert ptr or int to ptr |
|array| `001000` | `get`  | `type(arr[0])` | `arr` | index: `ptr/int` | load from array |
|| `000001` | - | -  |  - | -  | reserved |
|| `001010` | `set`  | array to modify: `arr` | value: `any` | index: `ptr/int` | store into array |
|| `001011` | `seti` | array to modify: `arr` | index: `ptr/int` | value: `any` | store into array |
|| `001100` | `len`  | length:`int` | `arr` | - | length of array |
|| `001101` | `alloc`  | `arr` | number of elements: `int` | fill value: `any` | creates an array of n elements and fills it with the given fill value |
|| `00111x` | - | -  |  - | -  | reserved |
|int| `010000` | `iadd`  | `int` | `int` | `int` | integer add |
|| `010001` | `isub`  | `int` | `int` | `int` | integer subtract |
|| `010010` | `iumul`  | `int` | `int` | `int` | unsigned integer multiply |
|| `010011` | `ismul`  | `int` | `int` | `int` | signed integer multiply |
|| `010100` | `ineg`  | `int` | - | `int` | negate signed integer |
|| `010101` | `islt`  | `int` | `int` | `int` | signed integer less than. outputs 1 for false and 0 for true |
|| `010110` | `isgt`  | `int` | `int` | `int` | signed integer greater than. outputs 1 for false and 0 for true |
|| `010111` | `iult`  | `int` | `int` | `int` | unsigned integer less than. outputs 1 for false and 0 for true |
|ptr| `010000` | `padd`  | `int` | `int` | `int` | ptr add |
|| `010001` | `psub`  | `int` | `int` | `int` | ptr subtract |
|| `010010` | `pumul`  | `int` | `int` | `int` | unsigned ptr multiply |
|| `010011` | - | - | - | - | reserved |
|| `010100` | - | - | - | - | reserved |
|| `010101` | `pult`  | `int` | `int` | `int` | unsigned ptr less than. outputs 1 for false and 0 for true |
|| `010110` | `pugt`  | `int` | `int` | `int` | unsigned ptr greater than. outputs 1 for false and 0 for true |
|| `010111` | - | - | - | - | reserved |

## pseudo ops
many comparisions ops that aren't builtin can be done via specific `xxx` and a `sub`
