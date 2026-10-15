#==============================================================================#
#======================= Check of necessary variables =========================#
#==============================================================================#
# The family-check block + RAL_TARGET_MCU decode chain are generated fresh
# by ral-link.sh's GenerateRalCMakeLists for every release - this line is
# replaced wholesale each run, never edited by hand.
# Target MCU family for active RAL modul
set(RAL_TARGET_MCU_TYPE "STM32F7xx")
# Check if correct RAL module is using
if(NOT ${RAL_TARGET_MCU_TYPE} STREQUAL ${MCU_FAMILY_ID})
    message(FATAL_ERROR "")
endif()

# RAL_TARGET_MCU decode chain, generated from CMSIS_ST/Include/stm32f7xx.h
# at release-composition time (ral-link.sh). Maps the raw part number the
# user passed in (${TARGET_MCU_FULL_NAME}) onto the exact device macro this
# release's CMSIS_ST actually defines - some lines (e.g. STM32G411) have no
# generic "xx" define at all, only flash-size-specific ones, so a plain
# suffix guess is not safe here.
if(TARGET_MCU_FULL_NAME MATCHES "^STM32F722")
    set(RAL_TARGET_MCU "STM32F722xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F723")
    set(RAL_TARGET_MCU "STM32F723xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F730")
    set(RAL_TARGET_MCU "STM32F730xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F732")
    set(RAL_TARGET_MCU "STM32F732xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F733")
    set(RAL_TARGET_MCU "STM32F733xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F745")
    set(RAL_TARGET_MCU "STM32F745xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F746")
    set(RAL_TARGET_MCU "STM32F746xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F750")
    set(RAL_TARGET_MCU "STM32F750xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F756")
    set(RAL_TARGET_MCU "STM32F756xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F765")
    set(RAL_TARGET_MCU "STM32F765xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F767")
    set(RAL_TARGET_MCU "STM32F767xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F769")
    set(RAL_TARGET_MCU "STM32F769xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F777")
    set(RAL_TARGET_MCU "STM32F777xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F779")
    set(RAL_TARGET_MCU "STM32F779xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F768")
    set(RAL_TARGET_MCU "STM32F769xx")
elseif(TARGET_MCU_FULL_NAME MATCHES "^STM32F778")
    set(RAL_TARGET_MCU "STM32F779xx")
else()
    message(FATAL_ERROR "TARGET_MCU_FULL_NAME [${TARGET_MCU_FULL_NAME}] does not match any device define known to the CMSIS_ST header for this release, stm32f7xx.h (checked: STM32F722xx STM32F723xx STM32F730xx STM32F732xx STM32F733xx STM32F745xx STM32F746xx STM32F750xx STM32F756xx STM32F765xx STM32F767xx STM32F769xx STM32F777xx STM32F779xx)")
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
    stm32f7xx_hal_conf_template.h
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
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_adc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_can.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_cec.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_cortex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_crc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_cryp.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_cryp_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dac.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dac_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dcmi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dcmi_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dfsdm.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dma_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_dsi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_eth.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_flash.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_flash_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_hash.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_hash_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_hcd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_i2c_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_i2s.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_irda.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_iwdg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_jpeg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_ltdc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_ltdc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_mdios.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_mmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_nand.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_nor.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_pcd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_pcd_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_pwr_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_qspi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_rcc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_rtc_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_sai.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_sai_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_sd.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_sdram.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_smartcard.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_smartcard_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_smbus.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_spdifrx.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_spi_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_sram.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_tim_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_uart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_uart_ex.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_hal_wwdg.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dac.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_fmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_sdmmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_usb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_utils.c
)

# Set public header files for the library
set(HAL_LL_PublicHeaders
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32_assert_template.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_adc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_can.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_cec.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_conf_template.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_crc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_cryp_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_cryp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dac_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dac.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dcmi_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dcmi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_def.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dfsdm.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dma_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_dsi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_eth.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_flash_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_flash.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_gpio_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_hash_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_hash.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_hcd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_i2c_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_i2s.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_irda_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_irda.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_jpeg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_ltdc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_ltdc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_mdios.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_mmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_nand.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_nor.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_pcd_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_pcd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_pwr_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_qspi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_rcc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_rtc_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_sai_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_sai.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_sd.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_sdram.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_smartcard_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_smartcard.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_smbus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_spdifrx.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_spi_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_sram.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_tim_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_tim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_uart_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_uart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_usart_ex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_usart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_hal_wwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_bus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_dac.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_fmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_sdmmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_system.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_tim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_usart.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_usb.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_utils.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Inc/stm32f7xx_ll_wwdg.h
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
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_adc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_crc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dac.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dma2d.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_dma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_exti.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_fmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_gpio.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_i2c.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_lptim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_pwr.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rcc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rng.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_rtc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_sdmmc.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_spi.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_tim.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_usart.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_usb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Stm32_Drv/Src/stm32f7xx_ll_utils.c
)

# Set public header files for the library
set(Ral_PublicHeaders       
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_adc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_bus.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_cortex.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_crc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dac.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dma2d.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_dma.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_exti.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_fmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_gpio.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_i2c.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_iwdg.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_lptim.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_pwr.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rcc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rng.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_rtc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_sdmmc.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_spi.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_system.h
    ${CMAKE_CURRENT_SOURCE_DIR}/RAL_ST/Port/Stm32_tim.h
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

