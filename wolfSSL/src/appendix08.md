<<<<<<< HEAD
# wolfSSL Porting Guide

## Purpose

This guide provides a reference for developers and engineers porting the wolfSSL lightweight SSL/TLS library to new embedded platforms, operating systems, or transport mediums (TCP/IP, bluetooth, etc.).  It calls out areas in the wolfSSL codebase which typically require modification when porting wolfSSL.  It should be considered a “guide” and as such, it is an evolving work.  If there is something you find missing, please let us know and we’ll be happy to add instructions or clarification to the document.


## Audience

This guide caters to developers or engineers porting the wolfSSL and wolfCrypt to new platforms or environments that are not supported by default.


## Introduction

Several steps need to be iterated through when getting wolfSSL to run on an embedded platform.  Some of these steps are outlined in [Section 2.4](chapter02.md#building-in-a-non-standard-environment).

Apart from steps in Chapter 2 of the wolfSSL Manual, there are areas in the code which may need porting or modifications in order to accommodate a specific platform.  wolfSSL abstracts many of these areas - attempting to make it as easy as possible to port wolfSSL to a new platform.

In the `./wolfssl/wolfcrypt/settings.h` file, there are several defines specific to different operating systems, TCP/IP stacks, and chipsets (ex: MBED, FREESCALE_MQX, MICROCHIP_PIC32, MICRIUM, EBSNET, etc.).  There are two main locations to put `#defines` when compiling and porting wolfSSL to a new platform:

1. New defines for a Operating System or TCP/IP stack port are typically added to the `settings.h` file when a new port of wolfSSL is completed.  This provides an easy way to turn on/off features as well as customize build settings that should be “default” for that build.  New custom defines can be added in this file when doing a port of wolfSSL to a new platform.  We encourage users to contribute ports of wolfSSL back to the master open source code branch on [GitHub](https://www.github.com/wolfssl/wolfssl).  This helps keep wolfSSL up to date and allows different ports to remain updated as the wolfSSL project improves and moves forward.

2. For users not wanting to contribute back their changes to wolfSSL proper, or for users who want to customize the wolfSSL build with additional preprocessor defines, wolfSSL recommends the use of a custom `user_settings.h` header file.  If `WOLFSSL_USER_SETTINGS` is defined when compiling the wolfSSL source files, wolfSSL will automatically include a custom header file called `user_settings.h`.  This header should be created by the user and placed on the include path.  This allows users to maintain one single file for their wolfSSL build, and makes it much easier to update to newer versions of wolfSSL.

wolfSSL encourages the submission of patches and code changes through either direct email ([facts@wolfssl.com](mailto:facts@wolfssl.com)), or through [GitHub pull request](https://github.com/wolfssl/wolfssl).


## Porting wolfSSL


### Data Types

**Q:  When do I need to read this section?**

A:  Setting the correct data type size for your platform is always important.

wolfSSL benefits speed-wise from having a 64-bit type available.  Define `SIZEOF_LONG` and `SIZEOF_LONG_LONG` to match the result of `sizeof(long)` and `sizeof(long long)` on your platform.  This can be added to a custom define in the `settings.h` file or to `user_settings.h`.  For example, in `settings.h` under a sample define of `MY_NEW_PLATFORM`:


```c
#ifdef MY_NEW_PLATFORM
	#define SIZEOF_LONG 4
	#define SIZEOF_LONG_LONG 8
	...
#endif
```


There are two additional data types used by wolfSSL and wolfCrypt, called `word32` and `word16`.  The default type mappings for these are:


```c
#ifndef WOLFSSL_TYPES
#ifndef byte
    typedef unsigned char  byte;
#endif
	typedef unsigned short word16;
    typedef unsigned int   word32;
    typedef byte           word24[3];
#endif
```


`word32` should be mapped to the compiler’s 32-bit type, and `word16` to the compiler’s 16-bit type.  If these default mappings are incorrect for your platform, you should define `WOLFSSL_TYPES` in `settings.h` or `user_settings.h` and assign your own custom typedefs for word32 and word16.

The fastmath library in wolfSSL uses the `fp_digit` and `fp_word` types.  By default these are mapped in `<wolfssl/wolfcrypt/tfm.h>` depending on build configuration.

`fp_word` should be twice the size of `fp_digit`.  If the default cases do not hold true for your platform, you should define `WOLFSSL_BIGINT_TYPES` in `settings.h` or `user_settings.h` and assign your own custom typedefs for `fp_word` and `fp_digit`.

wolfSSL does use a 64-bit type when available for some operations.  The wolfSSL build tries to detect and set up the correct underlying data type for `word64` based on what `SIZEOF_LONG` and `SIZEOF_LONG_LONG` have been set to.  On some platforms that don’t have a true 64-bit type, where two 32-bit types are used in conjunction, performance can be slow.  To compile out the use of 64-bit types, define `NO_64BIT`.


### Endianness

**Q:  When do I need to read this section?**

A:  Your platform is a big endian system.

Is your platform big endian or little endian?  wolfSSL defaults to a little endian system.  If your system is big endian, define `BIG_ENDIAN_ORDER` when building wolfSSL.  Example of setting this in `settings.h`:


```c
#ifdef MY_NEW_PLATFORM
	...
	#define BIG_ENDIAN_ORDER
	...
#endif
```



### writev

**Q:  When do I need to read this section?**

A:  `<sys/uio.h>` is not available.

By default, the wolfSSL API makes available `wolfSSL_writev()` to applications, which simulates `writev()` semantics.  On systems that don’t have the `<sys/uio.h>` header available, define `NO_WRITEV` to exclude this feature.


### Input / Output

**Q:  When do I need to read this section?**

A:  A BSD-style socket API is not available, you are using a custom transport layer or TCP/IP stack, or only want to use static buffers.

wolfSSL defaults to using a BSD-style socket interface.  If your transport layer provides a BSD socket interface, wolfSSL should integrate into it as-is, unless custom headers are needed.

wolfSSL provides a custom I/O abstraction layer which allows users to tailor wolfSSL's I/O functionality to their system.  Full details can be [found in Section 5.1.2](chapter05.md#custom-inputoutput-abstraction-layer).

Simply put, you can define `WOLFSSL_USER_IO`, then write your own I/O callback functions using wolfSSL's default `EmbedSend()` and `EmbedReceive()` as templates.  These two functions are located in `./src/io.c`.

wolfSSL uses dynamic buffers for input and output, which default to 0 bytes.  If an input record is received that is greater in size than the buffer, then a dynamic buffer is temporarily used to handle the request and then freed.

If you prefer using large, 16kB static buffers which will never need dynamic memory, you can enable this option by defining `LARGE_STATIC_BUFFERS`.

If dynamic buffers are used and the user requests an `wolfSSL_write()` that is bigger than the buffer size, then a dynamic block up to `MAX_RECORD_SIZE` is used to send the data. Users wishing to only send the data in chunks of the current buffer size at maximum, as defined by `RECORD_SIZE`, can do this by defining `STATIC_CHUNKS_ONLY`.  When using this define, `RECORD_SIZE` defaults to 128 bytes.


### Filesystem

**Q:  When do I need to read this section?**

A:  No file system is available, standard file system functions are not available, or you have a custom file system.

wolfSSL uses the filesystem for loading keys and certificates into the SSL session or context.  wolfSSL also allows loading these from memory buffers.  If strictly using memory buffers, a filesystem is not needed.

You can disable wolfSSL's usage of the filesystem by defining `NO_FILESYSTEM` when building the library.  This means that certificates and keys will need to be loaded from memory buffers instead of files.  An example of setting this in `settings.h`:


```c
#ifdef MY_NEW_PLATFORM
	...
	#define NO_FILESYSTEM
	...
#endif
```


Test key and certificate buffers can be found in the `./wolfssl/certs_test.h` header file.  These will match up to corresponding certificates and keys found in the `./certs` directory.

The `certs_test.h` header file can be updated using the `./gencertbuf.pl` script if needed.  Inside `gencertbuf.pl`, there are two arrays:  `fileList_1024` and `fileList_2048`.  Additional certificates or keys may be added to the respective array, depending on key size, and must be in DER format.  The above mentioned arrays map a certificate/key file location with the desired buffer name.  After modifying `gencertbuf.pl`, running it from the wolfSSL root directory will update the certificate and key buffers in `./wolfssl/certs_test.h`:


```sh
./gencertbuf.pl
```


If you would like to use a filesystem other than the default, the filesystem abstraction layer is located in `./wolfssl/wolfcrypt/wc_port.h`.  Here you will see filesystem ports for various platforms including EBSNET, FREESCALE_MQX, and MICRIUM.  You can add a custom define for your platform if needed - allowing you to define file system functions with `XFILE`, `XFOPEN`, `XFSEEK`, etc.  For example, the filesystem layer in `wc_port.h` for Micrium's µC/OS (MICRIUM) is as follows:


```c
#elif defined(MICRIUM)
#include <fs.h>
#define XFILE      FS_FILE*
#define XFOPEN     fs_fopen
#define XFSEEK     fs_fseek
#define XFTELL     fs_ftell
#define XREWIND    fs_rewind
#define XFREAD     fs_fread
#define XFCLOSE    fs_fclose
#define XSEEK_END  FS_SEEK_END
#define XBADFILE   NULL
```

### Threading

**Q:  When do I need to read this section?**

A:  You want to use wolfSSL in a multithreaded environment, or want to just compile it in single threaded mode.

If wolfSSL will only be used in a single threaded environment, the wolfSSL mutex layer can be disabled when compiling wolfSSL by defining `SINGLE_THREADED`.  This will negate the need to port the wolfSSL mutex layer.

If wolfSSL needs to be used in a multithreaded environment, the wolfSSL mutex layer will need to be ported to the new environment.  The mutex layer can be found in `./wolfssl/wolfcrypt/wc_port.h` and `./wolfcrypt/src/wc_port.c`.  `wolfSSL_Mutex` will need to be defined for the new system in `wc_port.h` and the mutex functions (`wc_InitMutex`, `wc_FreeMutex`, `wc_LockMutex` and `wc_UnLockMutex`) in `wc_port.c`.  You can search in `wc_port.h` and `wc_port.c` to see an example for some existing platform port layers (EBSNET, FREESCALE_MQX, etc.).


### Random Seed

**Q:  When do I need to read this section?**

A:  Either `/dev/random` or `/dev/urandom` is not available or you want to integrate into a hardware RNG.

By default, wolfSSL uses `/dev/urandom` or `/dev/random` to generate a RNG seed.  The `NO_DEV_RANDOM` define can be used when building wolfSSL to disable the default `GenerateSeed()` function.  If this is defined, you need to write a custom `GenerateSeed()` function in `./wolfcrypt/src/random.c`, specific to your target platform.  This allows you to seed wolfSSL’s PRNG with a hardware-based random entropy source if available.

For examples of how `GenerateSeed()` needs to be written, reference wolfSSL’s existing `GenerateSeed()` implementations in `./wolfcrypt/src/random.c`.


### Memory

**Q:  When do I need to read this section?**

A:  When you don’t have standard memory functions available or are interested in memory usage differences between optional math libraries.

wolfSSL proper uses both `malloc()` and `free()` by default.  When using the normal big integer math library, wolfCrypt will also use `realloc()`.

By default wolfSSL/wolfCrypt use the normal big integer math library, which uses quite a bit of dynamic memory.  When building wolfSSL, the fastmath library can be enabled, which is both faster and uses no dynamic memory for crypto operations (all on the stack).  By using fastmath, wolfSSL won't need a `realloc()` implementation at all.  As the SSL layer of wolfSSL still uses some dynamic memory, `malloc()` and `free()` are still required.

For a comparison of resource usage (stack/heap) between the big integer math library and fastmath library, ask us to see our Resource Use document.

To enable fastmath, define `USE_FAST_MATH` and build in `./wolfcrypt/src/tfm.c` instead of `./wolfcrypt/src/integer.c`.  Since the stack memory can be large when using fastmath, we recommend defining `TFM_TIMING_RESISTANT` as well.

If the normal `malloc()`, `free()`, and possibly `realloc()` functions are not available, define `XMALLOC_USER`, then provide custom memory function hooks in `./wolfssl/wolfcrypt/types.h` specific to the target environment.

Please [read section 5.1.1.1](chapter05.md#memory-use) for details about using `XMALLOC_USER`.


### Time

**Q:  When do I need to read this section?**

A:  When standard time functions (`time()`, `gmtime()`) are not available, or you need to specify a custom clock tick function.

By default, wolfSSL uses `time()`, `gmtime()`, and `ValidateDate()`, as specified in `./wolfcrypt/src/asn.c`.  These are abstracted to `XTIME`, `XGMTIME`, and `XVALIDATE_DATE`.  If the standard time functions, and `time.h`, are not available, the user can define `USER_TIME`.  After defining `USER_TIME`, the user can define their own `XTIME`, `XGMTIME`, and `XVALIDATE_DATE` functions.

wolfSSL uses `time(0)` by default for the clock tick function.  This is located in `./src/internal.c` inside of the `LowResTimer()` function.

Defining `USER_TICKS` allows the user to define their own clock tick function if `time(0)` is not wanted. The custom function needs second accuracy, but doesn’t have to be correlated to EPOCH.  See `LowResTimer()` function in `./src/internal.c` for reference.


### C Standard Library

**Q:  When do I need to read this section?**

A:  When you don’t have a C standard library available, or have a custom one.

wolfSSL can be built without the C standard library to provide a higher level of portability and flexibility to developers.  When doing so, the user needs to map functions they wish to use instead of the C standard ones.

Section 7, above, covered memory functions.  In addition to memory function abstraction, wolfSSL also abstracts string function and math functions, where the specific functions are typically abstracted to a define in the form of `X<FUNC>`, where `<FUNC>` is the name of the function being abstracted.

Please read [Section 5.1](chapter05.md) for details.


### Logging

**Q:  When do I need to read this section?**

A:  You want to enable debug messages but don’t have stderr available.

By default, wolfSSL provides debug output through stderr.  In order for debug messages to be enabled, wolfSSL must be compiled with `DEBUG_WOLFSSL` defined, and `wolfSSL_Debugging_ON()` must be called from the application code.  `wolfSSL_Debugging_OFF()` may be used by the application layer to turn off wolfSSL debug messages.

For environments which do not have stderr available, or wish to output debug messages over a different output stream or in a different format, wolfSSL allows applications to register a logging callback.

Please read [Section 8.1](chapter08.md) for details.


### Public Key Operations

**Q:  When do I need to read this section?**

A:  You want to use your own public key implementation with wolfSSL.

wolfSSL allows users to write their own public key callbacks which will be called when the SSL/TLS layer needs to do public key operations.  The user can optionally define 6 functions:



1. ECC sign callback
2. ECC verify callback
3. RSA sign callback
4. RSA verify callback
5. RSA encrypt callback
6. RSA decrypt callback

For full details, please read [Section 6.4](chapter06.md#public-key-callbacks).


### Atomic Record Layer Processing

**Q:  When do I need to read this section?**

A:  You want to do your own processing of record layers, specifically MAC/encrypt and decrypt/verify operations.

By default, wolfSSL handles record layer processing for the user using its cryptography library, wolfCrypt.  wolfSSL provides Atomic Record Processing callbacks for users who wish to have more control over MAC/encrypt and decrypt/verify functionality during the SSL/TLS connection.

The user will need to define 2 functions:

1. MAC/encrypt callback function
2. Decrypt/verify callback function

For full details, please read [Section 6.3](chapter06.md#user-atomic-record-layer-processing).


### Features

**Q:  When do I need to read this section?**

A:  When you want to disable features.

Features can be disabled when building wolfSSL by using the appropriate defines.  For a list of defines available, please refer to [Chapter 2](chapter02.md).


## Next Steps


### wolfCrypt Test Application

After getting wolfSSL proper to build on the target platform, a good next step is to port the wolfCrypt test application.  Running this application on the target system will verify that all the crypto algorithms are working correctly, using NIST test vectors.

If this step is skipped, and you instead proceed directly to establishing an SSL connection, it can be more difficult to debug problems caused by underlying crypto operations failing.

The wolfCrypt test application is located in `./wolfcrypt/test/test.c`.  If an embedded application has its own `main()` function, then `NO_MAIN_DRIVER` must be defined when compiling `./wolfcrypt/test/test.c`.  This will allow the application’s `main()` to call each cipher/algorithm test individually on its own.

If an embedded device does not have enough resources to run the entire wolfCrypt test application, individual tests can be broken out of `test.c` and compiled individually.  Please ensure that correct header files needed for the specific test case are included in the build when extracting isolated crypto tests from `test.c`.


## Support

General support questions may be sent directly to wolfSSL either through email, support forums, or wolfSSL’s Zendesk ticket tracking system.

Website:	   [https://www.wolfssl.com](https://www.wolfssl.com)

Support Email:  [support@wolfssl.com](mailto:support@wolfssl.com)

Zendesk: 	   [https://wolfssl.zendesk.com](https://wolfssl.zendesk.com)

Forums:  	   [https://www.wolfssl.com/forums](https://www.wolfssl.com/forums)

wolfSSL offers several support packages as well as consulting services to help users and customers port wolfSSL to new environments.

Support Packages:  [https://www.wolfssl.com/wolfSSL/Support/support_tiers.php](https://www.wolfssl.com/wolfSSL/Support/support_tiers.php)

Consulting Services: [https://www.wolfssl.com/wolfSSL/wolfssl-consulting.html](https://www.wolfssl.com/wolfSSL/wolfssl-consulting.html)

General Inquiries: [facts@wolfssl.com](mailto:facts@wolfssl.com)


=======
# wolfSSL Hardware Optimization Support Reference

## A. Architecture-Level ISA/SIMD Optimizations

| Architecture | Algorithms | user_settings.h Macro | configure Option | Notes |
|---|---|---|---|---|
| **Intel x86-64** | AES (all modes) | `WOLFSSL_AESNI` | `--enable-aesni` | AES-NI instructions |
| Intel x86-64 | SHA-256, SHA-512 | `USE_INTEL_SPEEDUP` + `HAVE_INTEL_AVX1` | Auto-detected | AVX1 assembly |
| Intel x86-64 | SHA-512, SHA-3 | `USE_INTEL_SPEEDUP` + `HAVE_INTEL_AVX2` | Auto-detected | AVX2 assembly |
| Intel x86-64 | ChaCha20, Poly1305 | `USE_INTEL_SPEEDUP` | Auto-detected | |
| Intel x86-64 | X25519 (Curve25519), Ed25519 | `USE_INTEL_SPEEDUP` | Auto-detected | fe_x25519_asm.S |
| Intel x86-64 | RSA/ECC/DH (SP) | `WOLFSSL_SP_X86_64_ASM` | Auto-detected | sp_x86_64_asm.S |
| **ARM AArch64** | AES, SHA-256/512, SHA-3, ChaCha20, Poly1305 | `WOLFSSL_ARMASM` | `--enable-armasm` | ARMv8 CE (Crypto Extension) |
| ARM AArch64 | X25519 (Curve25519), Ed25519 | `WOLFSSL_ARMASM` | `--enable-armasm` | armv8-curve25519.S |
| ARM AArch64 | RSA/ECC/DH (SP) | `WOLFSSL_SP_ARM64_ASM` | Auto-detected | sp_arm64.c |
| **ARM 32-bit (ARMv8-32)** | AES, SHA-256/512, SHA-3, ChaCha20, Poly1305 | `WOLFSSL_ARMASM` | `--enable-armasm` | |
| ARM 32-bit | X25519 (Curve25519), Ed25519 | `WOLFSSL_ARMASM` | `--enable-armasm` | armv8-32-curve25519.S |
| ARM 32-bit | RSA/ECC/DH (SP) | `WOLFSSL_SP_ARM32_ASM` | Auto-detected | sp_arm32.c |
| **ARM Thumb2** | X25519 (Curve25519), Ed25519 | `WOLFSSL_ARMASM` + `WOLFSSL_ARMASM_THUMB2` | `--enable-armasm` | thumb2-curve25519.S |
| **ARM Thumb** | RSA/ECC/DH (SP) | `WOLFSSL_SP_ARM_THUMB_ASM` | Auto-detected | sp_armthumb.c |
| **ARM Cortex-M** | RSA/ECC/DH (SP) | `WOLFSSL_SP_ARM_CORTEX_M_ASM` | Auto-detected | sp_cortexm.c |
| **RISC-V 64-bit** | AES, SHA-256/512, SHA-3, ChaCha20, Poly1305 | `WOLFSSL_RISCV_ASM` | `--enable-riscv` | riscv/riscv-64-*.c |
| **PowerPC 32-bit** | SHA-256 | `WOLFSSL_PPC32_ASM` | Auto-detected | ppc32/ppc32-sha256-asm.S |

## B. SP Math Library (Public Key Cryptography)

| Architecture | Algorithms | user_settings.h Macro | configure Option |
|---|---|---|---|
| x86-64 assembly | RSA 2048/3072/4096, ECC P-256/P-384/P-521, DH | `WOLFSSL_SP_X86_64_ASM` | Auto-detected |
| ARM AArch64 assembly | RSA, ECC, DH | `WOLFSSL_SP_ARM64_ASM` | Auto-detected |
| ARM 32-bit assembly | RSA, ECC, DH | `WOLFSSL_SP_ARM32_ASM` | Auto-detected |
| ARM Thumb assembly | RSA, ECC, DH | `WOLFSSL_SP_ARM_THUMB_ASM` | Auto-detected |
| Cortex-M assembly | RSA, ECC, DH | `WOLFSSL_SP_ARM_CORTEX_M_ASM` | Auto-detected |
| Generic C (32-bit / 64-bit) | RSA, ECC, DH | `WOLFSSL_SP_MATH` | `--enable-sp` |

### sp_int.c Inline Assembly Speedups

`sp_int.c` contains small inline assembly helpers for multi-precision multiply/add operations used when the dedicated per-algorithm SP files (e.g. `sp_x86_64.c`) are absent or for non-RSA/ECC key sizes. These are controlled by the following macros (auto-detected; can also be set in `user_settings.h`):

| Architecture | user_settings.h Macro |
|---|---|
| Intel x86-64 | `WOLFSSL_SP_X86_64` |
| Intel x86 (32-bit) | `WOLFSSL_SP_X86` |
| ARM AArch64 | `WOLFSSL_SP_ARM64` |
| ARM 32-bit | `WOLFSSL_SP_ARM32` |
| ARM Thumb | `WOLFSSL_SP_ARM_THUMB` |
| RISC-V 64-bit | `WOLFSSL_SP_RISCV64` |
| RISC-V 32-bit | `WOLFSSL_SP_RISCV32` |
| MIPS 64-bit | `WOLFSSL_SP_MIPS64` |
| MIPS 32-bit | `WOLFSSL_SP_MIPS` |
| s390x | `WOLFSSL_SP_S390X` |

### SP Key Size Configuration

```c
#define WOLFSSL_SP_MATH
#define WOLFSSL_SP_X86_64_ASM        /* x86-64 */
#define WOLFSSL_SP_ARM64_ASM         /* AArch64 */
#define WOLFSSL_SP_ARM_CORTEX_M_ASM  /* Cortex-M */

#define WOLFSSL_SP_2048  /* RSA-2048 */
#define WOLFSSL_SP_3072  /* RSA-3072 */
#define WOLFSSL_SP_4096  /* RSA-4096 */
#define WOLFSSL_SP_256   /* ECC P-256 */
#define WOLFSSL_SP_384   /* ECC P-384 */
#define WOLFSSL_SP_521   /* ECC P-521 */
```

## C. Microcontroller/SoC Chip-Specific Hardware Accelerators

| Vendor / Chip | Algorithms | user_settings.h Macro | configure Option | Source |
|---|---|---|---|---|
| **STMicroelectronics** | | | | |
| STM32F2/F4/F7/H7 (CRYP) | AES-ECB/CBC/CTR/GCM | `STM32_CRYPTO` | HAL auto-detected | port/st/stm32.c |
| STM32L4/L5/WB55 | AES, SHA (partial) | `STM32_CRYPTO` | HAL auto-detected | port/st/stm32.c |
| STM32U5 (DHUK) | AES with key wrapping | `WOLFSSL_STM32U5_DHUK` | Auto-detected | port/st/stm32.c |
| STM32MP1/MP13/MP25 | AES, SHA, RSA, ECC | `WOLFSSL_STM32_CRYPT` | Auto-detected | port/st/stm32.c |
| **NXP** | | | | |
| i.MX RT (DCP) | AES-ECB/CBC/CTR/GCM, SHA | `WOLFSSL_IMXRT_DCP` | Auto-detected | port/nxp/dcp_port.c |
| i.MX 6/8 (CAAM) | AES, SHA, RSA, ECC, DH, HMAC | `WOLFSSL_IMX6_CAAM` / `WOLFSSL_SECO_CAAM` | `--enable-caam` | port/caam/* |
| SE050 Secure Element | AES, SHA, RSA, ECC, X25519, ECDH | `WOLFSSL_SE050` + `WOLFSSL_SE050_CRYPT` | Auto-detected | port/nxp/se050_port.c |
| **Espressif** | | | | |
| ESP32 / ESP32-S2 / ESP32-S3 | AES, SHA-1/256/384/512, RSA, ECC | `WOLFSSL_ESP32_CRYPT` | `--enable-esp32` | port/Espressif/esp32_*.c |
| ESP32-C3 / C6 / H2 | AES, SHA-256/384/512 (partial) | `WOLFSSL_ESP32_CRYPT` | `--enable-esp32` | port/Espressif/esp32_*.c |
| **Renesas** | | | | |
| RX64M/RX71M/RX72M | SHA-1, SHA-256 | `WOLFSSL_RENESAS_RX64_HASH` | Auto-detected | port/Renesas/renesas_rx64_*.c |
| RX65N (SCE) | AES, SHA, RSA, ECC, RNG | `WOLFSSL_RENESAS_SCEPROTECT` | Auto-detected | port/Renesas/renesas_*.c |
| RZ/A1/A2 (TSIP) | AES, SHA, RSA, ECC, RNG | `WOLFSSL_RENESAS_TSIP` | Auto-detected | port/Renesas/renesas_tsip_*.c |
| RA4M4/RA6M4 (RSIP) | AES, SHA, RSA, ECC, RNG | `WOLFSSL_RENESAS_RSIP` | Auto-detected | port/Renesas/renesas_rsip*.c |
| RA FSP Secure Manager | AES, SHA, RSA, ECC, HMAC | `WOLFSSL_RENESAS_FSPSM` | Auto-detected | port/Renesas/renesas_fspsm_*.c |
| **Cypress** | | | | |
| PSoC 6 | AES, SHA, ECC, RNG | `WOLFSSL_PSOC6_CRYPTO` | Auto-detected | port/cypress/psoc6_crypto.c |
| **Silicon Labs** | | | | |
| EFR32 / EFM32 (SE) | AES, SHA, ECC, RNG | `WOLFSSL_SILABS_SE_ACCEL` | Auto-detected | port/silabs/silabs_*.c |
| **Maxim** | | | | |
| MAX3266X | AES-ECB/CBC, RSA | `WOLFSSL_MAX3266X` | Auto-detected | port/maxim/max3266x.c |
| MAXQ10XX | AES, SHA, RSA, ECC, RNG | `WOLFSSL_MAXQ10XX_CRYPTO` | `--with-maxq10xx=PART` | port/maxim/maxq10xx.c |
| **Microchip** | | | | |
| PIC32MZ | AES, SHA, RNG | `WOLFSSL_PIC32MZ_CRYPT` | Auto-detected | port/pic32/ |
| ATECC508A / 608A | AES, SHA, ECC (ECDH/ECDSA) | `WOLFSSL_ATECC` | Auto-detected | port/atmel/atmel.c |
| **Xilinx** | | | | |
| Zynq / Zynq-7000 | AES, SHA, RSA, ECC | `WOLFSSL_XILINX_CRYPT` | `--enable-xilinx` | port/xilinx/xil-*.c |
| Versal | AES-GCM, SHA-3, RNG | `WOLFSSL_XILINX_CRYPT_VERSAL` | `--enable-xilinx` | port/xilinx/xil-versal-*.c |
| **ARM CryptoCell** | | | | |
| CC-300/312/700/712/714 | AES, SHA, RSA, ECC, ChaCha, Poly1305 | `WOLFSSL_CRYPTOCELL` | `--enable-cryptocell` | port/arm/cryptocell*.c |
| **IoT-Safe (SIM)** | ECC, SHA, RNG | `WOLFSSL_IOTSAFE` | `--enable-iotsafe` | port/iotsafe/ |

## D. External Accelerators and Coprocessors

| Product | Algorithms | user_settings.h Macro | configure Option |
|---|---|---|---|
| Intel QuickAssist (QAT) | AES, RSA, DH, ECC, SHA | `HAVE_INTEL_QA` | `--with-intelqa=PATH` |
| Intel QAT (synchronous) | Same as above | `ENABLED_INTEL_QA_SYNC` | `--with-intelqa-sync=PATH` |
| Cavium NITROX | AES, RSA, DH, ECC, SHA | `HAVE_CAVIUM` | `--with-cavium=PATH` |
| Cavium NITROX V | Same as above | `HAVE_CAVIUM_V` | `--with-cavium=PATH` |
| Cavium Octeon (MIPS) | AES, RSA, ECC, SHA | `HAVE_CAVIUM_OCTEON` | `--with-octeon-sync=PATH` |
| NXP CAAM | AES, SHA, RSA, ECC, HMAC, DH | `WOLFSSL_CAAM` | `--enable-caam` |

## E. OS Kernel Hardware Offload Interfaces

| Interface | Algorithms | user_settings.h Macro | configure Option |
|---|---|---|---|
| Linux `/dev/crypto` | AES, SHA-1/256/512, RSA, ECC, DH, HMAC | `WOLFSSL_DEVCRYPTO` | `--enable-devcrypto` |
| Linux Kernel Crypto API | AES, SHA, RSA, ECC, X25519, HMAC | `WOLFSSL_KCAPI` | `--enable-kcapi` |
| Linux AF_ALG | AES, SHA (kernel backend dependent) | `WOLFSSL_AFALG` | `--enable-afalg` |
| PSA (Platform Security Architecture) | Vendor implementation dependent | `WOLFSSL_HAVE_PSA` | `--enable-psa` |
>>>>>>> 5d885e1... Appendix Hardware optimization
