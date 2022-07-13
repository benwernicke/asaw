# asaw
async await system for c

## Example Usage
```
#include "asaw.h"
#include <stdio.h>
#include <stdint.h>
#include <assert.h>


void* hello(void* a)
{
    printf("Hello from task: %lu\n", (uintptr_t)a);
    return NULL;
}

#define NUM_TASKS 100

int main(void)
{
    // initialize system, may return err code
    int err = asaw_init(8);
    assert(err == 0);

    future_t* futures[NUM_TASKS];

    uint64_t i;
    for(i = 0; i < NUM_TASKS; i++) {

        // async returns a future_t* that can later be awaited
        // every future_t* needs to be awaited
        futures[i] = async(hello, (void*)i);

    }

    for(i = 0; i < NUM_TASKS; i++) {

        // await future_t* this returns the return of the function
        void* res = await(futures[i]);
        assert(res == NULL);

    }

    for(i = 0; i < NUM_TASKS; i++) {

        // just calls the function asynchronisly without future
        async_noawait(hello, (void*)i);

    }

    // free system
    asaw_free();
    return 0;
}
```

## Function Documentation

```
int asaw_init(uint_fast16_t num_threads);
```
The `asaw_init` function will initialize the async/await system. It may return nonzero on error.

```
void asaw_free(void);
```
The `asaw_free` function free's every memory associated with the async/await system and join the respective threads.

```
future_t* async(thread_function_t fn, void* arg);
```
The `async` function calls the `fn` asynchronously and returns a `future_t*` to later retrieve the return value via `await`.

```
void async_noawait(thread_function_t fn, void* arg);
```
The `async_noawait` function calls the `fn` asynchronously without returning any handle for the return value.

```
void* await(future_t* f);
```
The `await` function waits for the completion of the given `future_t*` and returns the associated return value.
