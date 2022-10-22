# Resource Constraints STM32F411

## Pointer Values

### Printed
| Variable | Pointer dec | Pointer hex |
|-|-|-|
| Uninitialized global variable | `536871088` | `0x200000b0` |
| Initialized global variable | `536871092` | `0x200000b4` |
| Static variable inside a function | `536871096` | `0x200000b8` |
| Variable inside a function | `537001920` | `0x2001FFC0` |
| Variable on the Heap | `536889688` | `0x20004958` |

### The linker Map

```
                0x0000000020020000                _estack = (ORIGIN (RAM) + LENGTH (RAM))
```

```
 .bss.global_uninit
                0x00000000200000b0        0x4 ./Core/Src/exercise_7.o
                0x00000000200000b0                global_uninit
 .bss.global_init
                0x00000000200000b4        0x4 ./Core/Src/exercise_7.o
                0x00000000200000b4                global_init
 .bss.static_in_fn.0
                0x00000000200000b8        0x4 ./Core/Src/exercise_7.o
```

```
._user_heap_stack
                0x0000000020004950      0x600 load address 0x000000000800b7fc
                0x0000000020004950                . = ALIGN (0x8)
                [!provide]                        PROVIDE (end = .)
                0x0000000020004950                PROVIDE (_end = .)
                0x0000000020004b50                . = (. + _Min_Heap_Size)
 *fill*         0x0000000020004950      0x200 
                0x0000000020004f50                . = (. + _Min_Stack_Size)
 *fill*         0x0000000020004b50      0x400 
                0x0000000020004f50                . = ALIGN (0x8)
```

### After modifying linker (swapping init and uninit)

| Variable | Pointer dec | Pointer hex |
|-|-|-|
| Uninitialized global variable | `536870940` | `0x2000001C` |
| Initialized global variable | `536870944` | `0x20000020` |

## Conclusion
Given that the linker map file indicates that the stack starts at `0x20020000` and the variable placed on the stack is at `0x2001FFC0` the stack is growing in the negative direction towards the heap located around `0x20004950` and growing in the positive direction. This means that the stack and the heap have a maximum combined size of `112304` bytes for the default CubeIDE configuration for the STM32F411.