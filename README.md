# stm32f103zetx_rtc_fix

hello everybody.

this repository is a modified version of stm32cubemx generated code for stm32f103zetx, that the RTC date saving problem is fixed in it. i have tested it and it works correctly for me. 
please note that in this solution the (RTC_BKP_DR2, RTC_BKP_DR3 and RTC_BKP_DR4) backup data registers are used for saving the (year, month and day) and you should not use them to save something else in your code. 

##### if you want to fix your stm32cubemx generated project, this is the procedure and i think it works on all stm32f1xx microcontrollers:

**1. in the main.c file, find the MX_RTC_Init function and do the followings:**

 First find 
    
    if (HAL_RTC_Init(&hrtc) != HAL_OK)
    {
        _Error_Handler(__FILE__, __LINE__);
    }
	
 and if it is located inside, if(HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR1) != someHEX) block, get it out of the if block.

 Then add the following, else block right after, if(HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR1) != someHEX)
    
    else
    {
        hrtc.DateToUpdate.Year = HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR2);
        hrtc.DateToUpdate.Month = HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR3);
        hrtc.DateToUpdate.Date = HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR4);
        HAL_RTC_GetTime(&hrtc, &time, RTC_FORMAT_BIN);
    }
    
**2. in the stm32f1xx_hal_rtc.c file, find the HAL_RTC_SetDate function and do the followings:**

  find comment /* WeekDay set by user can be ignored because automatically calculated */ and add the following lines right before it:

    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR2,hrtc->DateToUpdate.Year);
    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR3,hrtc->DateToUpdate.Month);
    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR4,hrtc->DateToUpdate.Date);
    
**3. in the stm32f1xx_hal_rtc.c file, find the RTC_DateUpdate function and do the followings:**

 find comment /* Update day of the week */ and add the following lines right before it:

    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR2,hrtc->DateToUpdate.Year);
    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR3,hrtc->DateToUpdate.Month);
    HAL_RTCEx_BKUPWrite(hrtc,RTC_BKP_DR4,hrtc->DateToUpdate.Date);
    

**that's it.**
    

