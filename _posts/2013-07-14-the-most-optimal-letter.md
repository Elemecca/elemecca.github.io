---
title: The Most Optimal Letter
date: 2013-07-14 01:22:41.000000000 -07:00
tags:
- embedded systems
---

GCC has an interesting take on optimizing a localization table:

    7816>?
     WenWraW                W               WroWocWl WntWraWtiWn
     W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--W--
    W?      WhiW hWlpW              W0)WLiWt WurWenW mWcrWs
     WX/WX  WonWerWs W/rWveWseWX    W(xW    MWcrW x
    W~      WelWteWt        W       [WStWrt
    W#      WesWt WheWBPW  W                W       SWop
    W$      WumW tW bWotWoaWerW     {WStWrtWwiWh WeaW
     &W%    WelWy W uW/mW           W}      WtoW
     aWA/W  AWXPWN WloW/HW/RWADW            WabW"   WenW sWriWg
     W      SWt WauWraWe    W       1W3
     W/CWAUW aWsiWnmWntW(aWx/WS)W   0W12W
     dWD    WeaWurW AWC WonWe/WONW.)W0bW10WSeWd WalWe
     W      MWasWreWfrWquWncW               W       RWad
    Wg/W    GWneWatW PWM/WerWo      W/      WLKWhi
    Wh      WomWanWhiWtoWy  W       \WCLW lW
     iWVeWsiWniWfoWstWtuWinWo       W^      WLKWtiWk
     W/LWBiWorWerW(mWb/WSBW         W       DWT Wi
     W      CWanWe WodW             W_      WATWlo
    Wo      WetWouWpuW tWpeW                W       DWT WeaW
     pWP    WulWupWreWisWorW (WffWONW       !WBiW rWad
    Ws      WcrWptWenWinW           W:      WepWatWe.W. W:1W
     vWShWw WolWs/WtaWesW   ;WBiWs Wo WeaW/wWitW eWg.W0xW5;W
     wWW    WSUW(oWf/WN)W   /Wx=W>/W0>WUsWrmWcrW xWasWigW xWliWt Wll

That only happens at `-O1` or higher; with optimization off it comes out fine.
