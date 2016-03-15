PROJECT(mbedtls C)

SET(TARGET_LIB "mbedtls")

SET(MBEDTLS_SRC_CRYPTO
    library/aes.c
    library/aesni.c
    library/arc4.c
    library/asn1parse.c
    library/asn1write.c
    library/base64.c
    library/bignum.c
    library/blowfish.c
    library/camellia.c
    library/ccm.c
    library/cipher.c
    library/cipher_wrap.c
    library/ctr_drbg.c
    library/des.c
    library/dhm.c
    library/ecdh.c
    library/ecdsa.c
    library/ecjpake.c
    library/ecp.c
    library/ecp_curves.c
    library/entropy.c
    library/entropy_poll.c
    library/error.c
    library/gcm.c
    library/havege.c
    library/hmac_drbg.c
    library/md.c
    library/md2.c
    library/md4.c
    library/md5.c
    library/md_wrap.c
    library/memory_buffer_alloc.c
    library/oid.c
    library/padlock.c
    library/pem.c
    library/pk.c
    library/pk_wrap.c
    library/pkcs12.c
    library/pkcs5.c
    library/pkparse.c
    library/pkwrite.c
    library/platform.c
    library/ripemd160.c
    library/rsa.c
    library/sha1.c
    library/sha256.c
    library/sha512.c
    library/threading.c
    library/timing.c
    library/version.c
    library/version_features.c
    library/xtea.c
)

SET(MBEDTLS_SRC_X509
    library/certs.c
    library/pkcs11.c
    library/x509.c
    library/x509_create.c
    library/x509_crl.c
    library/x509_crt.c
    library/x509_csr.c
    library/x509write_crt.c
    library/x509write_csr.c
)

SET(MBEDTLS_SRC_TLS
    library/debug.c
    library/net.c
    library/ssl_cache.c
    library/ssl_ciphersuites.c
    library/ssl_cli.c
    library/ssl_cookie.c
    library/ssl_srv.c
    library/ssl_ticket.c
    library/ssl_tls.c
)

FILE(GLOB MBEDTLS_PUBLIC_HEADERS ${MBEDTLS_SOURCE_DIR}/include/mbedtls/*.h)

# Dependent include files
INCLUDE_DIRECTORIES(${MBEDTLS_SOURCE_DIR}/include)

IF(MSVC)
    SET(CMAKE_DEBUG_POSTFIX "d")
ENDIF(MSVC)

ADD_LIBRARY(${TARGET_LIB} STATIC ${MBEDTLS_SRC_CRYPTO} ${MBEDTLS_SRC_X509} ${MBEDTLS_SRC_TLS})
SET_TARGET_PROPERTIES(${TARGET_LIB} PROPERTIES OUTPUT_NAME mbedtls)
TARGET_LINK_LIBRARIES(${TARGET_LIB} ${MBEDX509_STATIC_TARGET})

# Runtime equals DLL-files
# Archive equals static libraries
# Library equals module libraries
IF(NOT SKIP_INSTALL_LIBRARIES AND NOT SKIP_INSTALL_ALL )
    install(TARGETS ${TARGET_LIB}
        RUNTIME DESTINATION ${EXECUTABLE_OUTPUT_PATH}
        LIBRARY DESTINATION ${LIBRARY_OUTPUT_PATH}
        ARCHIVE DESTINATION ${ARCHIVE_OUTPUT_PATH})
ENDIF()
IF(NOT SKIP_INSTALL_HEADERS AND NOT SKIP_INSTALL_ALL )
    install(FILES ${MBEDTLS_PUBLIC_HEADERS} DESTINATION ${INCLUDE_OUTPUT_PATH}/mbedtls)
ENDIF()
