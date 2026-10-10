#==============================================================================#
#======================= Check of necessary variables =========================#
#==============================================================================#
# The family-check block + RAL_TARGET_MCU decode chain are generated fresh
# by ral-link.sh's GenerateRalCMakeLists for every release - this line is
# replaced wholesale each run, never edited by hand.
# Target MCU family for active RAL modul
set(RAL_TARGET_MCU_TYPE "STM32H7RSxx")
# Check if correct RAL module is using
if(NOT ${RAL_TARGET_MCU_TYPE} STREQUAL ${MCU_FAMILY_ID})
    message(FATAL_ERROR "")
endif()

# RAL_TARGET_MCU decode chain, generated from CMSIS_ST/Include/stm32h7rsxx.h
# at release-composition time (ral-link.sh). Maps the raw part number the
# user passed in (${TARGET_MCU_FULL_NAME}) onto the exact device macro this
# release's CMSIS_ST actually defines - some lines (e.g. STM32G411) have no
# generic "xx" define at all, only flash-size-specific ones, so a plain
# suffix guess is not safe here.
if(TARGET_MCU_FULL_NAME MATCHES "^STM32H7R3")
    set(RAL_TARGET_MCU "STM32H7R3xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32H7R7")
    set(RAL_TARGET_MCU "STM32H7R7xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32H7S3")
    set(RAL_TARGET_MCU "STM32H7S3xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32H7S7")
    set(RAL_TARGET_MCU "STM32H7S7xx")
else()
    message(FATAL_ERROR "TARGET_MCU_FULL_NAME [${TARGET_MCU_FULL_NAME}] does not match any device define known to the CMSIS_ST header for this release, stm32h7rsxx.h (checked: STM32H7R3xx STM32H7R7xx STM32H7S3xx STM32H7S7xx)")
endif()

# Check if target MCU is configured
if(DEFINED RAL_TARGET_MCU AND NOT RAL_TARGET_MCU STREQUAL "")
    message(STATUS "RAL_TARGET_MCU is set to ${RAL_TARGET_MCU}")
else()
    message(FATAL_ERROR "RAL_TARGET_MCU is not set or empty!")
endif()
# Header templates copied into the build directory and renamed (strips "_template")
set(RAL_CONF_TEMPLATE_FILES
    stm32_assert_template.h
    stm32h7rsxx_hal_conf_template.h
)
foreach(templateFileName ${RAL_CONF_TEMPLATE_FILES})
    string(REPLACE "_template" "" targetFileName "${templateFileName}")
    configure_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/${templateFileName}"
        "${CMAKE_BINARY_DIR}/${targetFileName}"
        COPYONLY
    )
endforeach()
# Add build folder to include paths
include_directories(${CMAKE_BINARY_DIR})

#==============================================================================#
#===================== Library HAL_LL_Lib configuration =======================#
#==============================================================================#

# Set necessary definitions for the library
set(HAL_LL_Defines
    -D${RAL_TARGET_MCU}
)

# Set source files for library generation
set(HAL_LL_LibSrcs
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_adc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_cec.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_cordic.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_cortex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_crc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_cryp.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_cryp_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_dcmipp.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_dma_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_dts.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_eth.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_eth_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_fdcan.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_flash.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_flash_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_gfxmmu.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_gfxtim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_gpu2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_hash.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_hcd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_i2c_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_i2s.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_i2s_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_i3c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_icache.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_irda.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_iwdg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_jpeg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_ltdc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_ltdc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_mce.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_mdf.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_mdios.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_mmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_mmc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_nand.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_nor.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pcd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pcd_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pka.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pssi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_pwr_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_ramecc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rcc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rng_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_rtc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sai.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sai_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sd_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sdio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sdram.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_smartcard.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_smartcard_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_smbus.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_smbus_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_spdifrx.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_spi_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_sram.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_tim_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_uart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_uart_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_usart_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_wwdg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_hal_xspi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_cordic.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_crs.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dlyb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_fmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_i3c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_lpuart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_pka.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_sdmmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_ucpd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_usb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_utils.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_util_i3c.c
)

# Set public header files for the library
set(HAL_LL_PublicHeaders
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32_assert_template.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_adc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_cec.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_conf_template.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_cordic.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_crc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_cryp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_cryp_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_dcmipp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_def.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_dma_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_dts.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_eth.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_eth_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_fdcan.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_flash.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_flash_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_gfxmmu.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_gfxtim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_gpio_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_gpu2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_hash.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_hcd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_i2c_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_i2s.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_i2s_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_i3c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_icache.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_irda.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_irda_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_jpeg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_ltdc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_ltdc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_mce.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_mdf.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_mdios.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_mmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_mmc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_nand.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_nor.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pcd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pcd_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pka.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pssi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_pwr_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_ramecc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rcc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rng_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_rtc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sai.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sai_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sd_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sdio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sdram.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_smartcard.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_smartcard_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_smbus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_smbus_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_spdifrx.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_spi_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_sram.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_tim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_tim_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_uart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_uart_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_usart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_usart_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_wwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_hal_xspi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_bus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_cordic.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_crs.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_dlyb.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_fmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_i3c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_icache.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_lpuart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_pka.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_sdmmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_system.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_tim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_ucpd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_usart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_usb.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_utils.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_ll_wwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32h7rsxx_util_i3c.h
)

#==============================================================================#
#======================= Library Ral_Lib configuration ========================#
#==============================================================================#

# Set necessary definitions for the library
set(Ral_Defines
    -D${RAL_TARGET_MCU}
)

# Set source files for library generation
set(Ral_LibSrcs     
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_cordic.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_crs.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dlyb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_fmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_i3c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_lpuart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_pka.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_sdmmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_ucpd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_usb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32h7rsxx_ll_utils.c
)

# Set public header files for the library
set(Ral_PublicHeaders       
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_bus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_cordic.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_crs.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dlyb.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_fmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_i3c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_icache.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_lpuart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_pka.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_sdmmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_system.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_tim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_ucpd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_usart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_usb.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_utils.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_wwdg.h
)

#==============================================================================#
#======================= Library HAL_LL_Lib generation ========================#
#==============================================================================#

add_library(HAL_LL_Lib STATIC ${HAL_LL_LibSrcs})

# Specify the public headers for the library
set_target_properties(HAL_LL_Lib PROPERTIES PUBLIC_HEADER "${HAL_LL_PublicHeaders}")

target_include_directories(HAL_LL_Lib 
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS/CMSIS/Core/Include # Cortex core functionality
        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS/CMSIS/DSP/Include  # DSP functionality
        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS_ST/Include         # ST CMSIS Device files
        ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc     # HAL and LL drivers
        ${CMAKE_BINARY_DIR}                                  # Copied *_hal_conf.h (from *_template.h)
    PRIVATE

    INTERFACE
)

target_link_libraries(HAL_LL_Lib
    PUBLIC
    
    PRIVATE
    
    INTERFACE
)

target_compile_definitions(HAL_LL_Lib 
    PUBLIC
        ${HAL_LL_Defines}
    
    PRIVATE
    
    INTERFACE
)

message(STATUS "ST's HAL/LL library (HAL_LL_Lib) created")

#==============================================================================#
#========================= Library Ral_Lib generation =========================#
#==============================================================================#

add_library(Ral_Lib STATIC ${Ral_LibSrcs})

# Specify the public headers for the library
set_target_properties(Ral_Lib PROPERTIES PUBLIC_HEADER "${Ral_PublicHeaders}")

target_include_directories(Ral_Lib 
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port

        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS/CMSIS/Core/Include # Cortex core functionality
        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS/CMSIS/DSP/Include  # DSP functionality
        ${CMAKE_CURRENT_SOURCE_DIR}/CMSIS_ST/Include         # ST CMSIS Device files
        ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc     # HAL and LL drivers
    PRIVATE

    INTERFACE
)

target_link_libraries(Ral_Lib
    PUBLIC
    
    PRIVATE
    
    INTERFACE
)

target_compile_definitions(Ral_Lib 
    PUBLIC
        ${Ral_Defines}
    
    PRIVATE
    
    INTERFACE
)

message(STATUS "Register Abstraction Layer (RAL) library (Ral_Lib) created")

