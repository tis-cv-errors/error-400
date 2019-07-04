# Demo-caesar

A version of the *demo-caesar* repository for the purposes of testing the
*TiS-CI* prototypes.


## Command line

### Command to simply run test `$N` with `tis-interpreter`

```bash
tis-interpreter -tis-config-load tis.config -tis-config-select $N
```

### Full TiS-CI command for test `$N`

```bash
tis-interpreter\
 --no-color\
 -tis-config-restrict\
 -tis-config-load tis.config\
 -tis-config-select $N\
 --keep-results\
 -info-json-results test-results-$N.json
```

### Variables

Test numbers: `$N` = [1..9]


## Test suite

* Files `src/caesar.c` and `src/gen_test.c` must be always used.
* The `src` directory must be included with `-I src` in order to get headers
  `src/caesar.h` and `src/gen_test.h`.
* There are 3 test cases, each available in two forms:
  * Separate files `tests/test_01.c`, `tests/test_02.c`, and `tests/test_03.c`,
    each with a singl entry point `main`.
  * Single file `tests/three_tests.c` with three entry points `test_01`,
    `test_02`, and `test_03`.

### Tests cases

1. No undefined behavior.

   Exit value: **0**

   Result: **OK**

   *(Using file `tests/test_01.c`.)*

2. Undefined behavior (out of bounds write).

   Exit value: **0**

   Result: **UB**

   > src/caesar.c:30:[kernel] warning: out of bounds write.
     assert \valid(buf+i);

   *(Using file `tests/test_02.c`.)*

3. Undefined behavior (signed overflow).

   Exit value: **0**

   Result: **UB**

   > src/caesar.c:16:[kernel] warning: signed overflow.
     assert -x ≤ 2147483647;

   *(Using file `tests/test_03.c`.)*

4. Same as **1.**

   *(Using file `tests/three_tests.c` with option `main` = `test_01`.)*

5. Same as **2.**

   *(Using file `tests/three_tests.c` with option `main` = `test_02`.)*

6. Same as **3.**

   *(Using file `tests/three_tests.c` with option `main` = `test_03`.)*

7. No entry point.

   Exit value: **1**

   Result: **error** (`user error`)

   > [kernel] user error: cannot find entry point 'main'.

8. Parsing error.

   Exit value: **0**

   Result: **error** (`parsing error`)

   > tests/test_parsing_error.c:3:[kernel] user error: syntax error at
     columns 0:1: declaration expected after typedef name

9. The `compilation_cmd` = `-I src` option missing: header file not included.

   Exit value: **0**

   Result: **error** (`parsing error`)

   > src/gen_test.c:3:20: fatal error: caesar.h: No such file or directory
