# Can you handle these files?
### TEAM NAME: NYCP

## CATEGORY
Forensics

## DESCRIPTION


## FLAG
`WH2021{iSEEuHANDLEDthisWELL}`

## WRITEUP
First step of Volatility is always to run the `imageinfo` command so that we know what profile to use.

```
$ volatility -f memdump.mem imageinfo
Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/kali/Downloads/memdump.mem)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80002bf70a0L
          Number of Processors : 2
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002bf8d00L
                KPCR for CPU 1 : 0xfffff880009ef000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2021-02-20 14:29:46 UTC+0000
     Image local date and time : 2021-02-20 22:29:46 +0800
```

We can use the first suggested profile `Win7SP1x64`.

We then run `pstree` to know what processes were running when the memory image was created.

```
$ volatility -f memdump.mem --profile Win7SP1x64 pstree
Volatility Foundation Volatility Framework 2.6
Name                                                  Pid   PPid   Thds   Hnds Time
-------------------------------------------------- ------ ------ ------ ------ ----
 0xfffffa80039f0060:wininit.exe                       420    348      3     78 2021-02-20 13:20:56 UTC+0000
. 0xfffffa800513f390:lsass.exe                        524    420      8    580 2021-02-20 13:20:56 UTC+0000
. 0xfffffa8005143460:lsm.exe                          536    420     10    146 2021-02-20 13:20:56 UTC+0000
. 0xfffffa800512d060:services.exe                     476    420      7    209 2021-02-20 13:20:56 UTC+0000
.. 0xfffffa8005151b30:svchost.exe                     648    476     11    372 2021-02-20 13:20:57 UTC+0000
... 0xfffffa8005535920:WmiPrvSE.exe                  1888    648     10    221 2021-02-20 13:20:59 UTC+0000
.. 0xfffffa8005425b30:svchost.exe                    1176    476     19    321 2021-02-20 13:20:58 UTC+0000
.. 0xfffffa80052ca6f0:svchost.exe                     884    476     20    446 2021-02-20 13:20:57 UTC+0000
... 0xfffffa800572e5c0:dwm.exe                       2304    884      5    137 2021-02-20 13:21:03 UTC+0000
.. 0xfffffa800274f060:sppsvc.exe                      416    476      4    154 2021-02-20 13:22:59 UTC+0000
.. 0xfffffa8005258810:svchost.exe                     752    476      8    284 2021-02-20 13:20:57 UTC+0000
.. 0xfffffa80053034c0:svchost.exe                     936    476     47   1096 2021-02-20 13:20:57 UTC+0000
.. 0xfffffa8005730060:svchost.exe                    2908    476     13    333 2021-02-20 13:22:59 UTC+0000
.. 0xfffffa800548f5b0:dllhost.exe                    3756    476     13    187 2021-02-20 13:24:02 UTC+0000
.. 0xfffffa80054e1740:svchost.exe                    1332    476     17    275 2021-02-20 13:20:58 UTC+0000
.. 0xfffffa800528bb30:svchost.exe                     824    476     20    477 2021-02-20 13:20:57 UTC+0000
... 0xfffffa8004339060:audiodg.exe                   2448    824      6    136 2021-02-20 14:12:44 UTC+0000
.. 0xfffffa800557d540:vmtoolsd.exe                   1460    476     11    277 2021-02-20 13:20:58 UTC+0000
.. 0xfffffa8005633060:svchost.exe                    1724    476      8     99 2021-02-20 13:20:59 UTC+0000
.. 0xfffffa8005241060:vm3dservice.ex                  716    476      3     44 2021-02-20 13:20:57 UTC+0000
.. 0xfffffa8005347890:svchost.exe                     312    476     13    580 2021-02-20 13:20:57 UTC+0000
.. 0xfffffa80051d2960:svchost.exe                     984    476     17    502 2021-02-20 13:20:57 UTC+0000
.. 0xfffffa80055955a0:taskhost.exe                   2148    476      8    195 2021-02-20 13:21:02 UTC+0000
.. 0xfffffa80054f7b30:VGAuthService.                 1404    476      4     93 2021-02-20 13:20:58 UTC+0000
.. 0xfffffa80056bcb30:msdtc.exe                      2028    476     12    147 2021-02-20 13:20:59 UTC+0000
.. 0xfffffa80053b5b30:spoolsv.exe                    1140    476     13    280 2021-02-20 13:20:58 UTC+0000
.. 0xfffffa800395b390:SearchIndexer.                 2680    476     13    682 2021-02-20 13:21:10 UTC+0000
 0xfffffa8004f0bb30:csrss.exe                         368    348      9    469 2021-02-20 13:20:56 UTC+0000
 0xfffffa8005506810:explorer.exe                     2332   2288     30    912 2021-02-20 13:21:03 UTC+0000
. 0xfffffa8002990920:notepad.exe                     3800   2332      1     61 2021-02-20 14:17:11 UTC+0000
. 0xfffffa8005315060:chrome.exe                      1692   2332     31    860 2021-02-20 14:17:19 UTC+0000
.. 0xfffffa800291e630:chrome.exe                     3404   1692     13    203 2021-02-20 14:17:23 UTC+0000
.. 0xfffffa8005942b30:chrome.exe                     3424   1692     13    204 2021-02-20 14:17:40 UTC+0000
.. 0xfffffa800267a820:chrome.exe                     2592   1692      7    132 2021-02-20 14:17:19 UTC+0000
.. 0xfffffa8002b409e0:chrome.exe                     1224   1692     19    248 2021-02-20 14:17:48 UTC+0000
.. 0xfffffa8004335930:chrome.exe                     3212   1692      9    233 2021-02-20 14:17:19 UTC+0000
.. 0xfffffa8002a6a720:chrome.exe                     2404   1692     14    216 2021-02-20 14:17:19 UTC+0000
.. 0xfffffa8002b262a0:chrome.exe                     2664   1692      8     89 2021-02-20 14:17:19 UTC+0000
. 0xfffffa80039481a0:notepad.exe                     3380   2332      1     61 2021-02-20 14:17:12 UTC+0000
. 0xfffffa80026b27d0:FTK Imager.exe                  3440   2332     16    325 2021-02-20 14:29:31 UTC+0000
. 0xfffffa800426c6a0:notepad.exe                     3352   2332      1     61 2021-02-20 14:16:43 UTC+0000
. 0xfffffa80057beb30:vmtoolsd.exe                    2496   2332      7    249 2021-02-20 13:21:04 UTC+0000
.. 0xfffffa80026d2150:VMToolsHookPro                 2900   2496      1     30 2021-02-20 14:05:17 UTC+0000
. 0xfffffa8002627640:notepad.exe                     3164   2332      1     61 2021-02-20 14:17:10 UTC+0000
. 0xfffffa8005831060:vm3dservice.ex                  2488   2332      2     56 2021-02-20 13:21:04 UTC+0000
 0xfffffa80024b5740:System                              4      0     92    548 2021-02-20 13:20:55 UTC+0000
. 0xfffffa800445eb30:smss.exe                         272      4      2     30 2021-02-20 13:20:55 UTC+0000
 0xfffffa80050ed060:csrss.exe                         432    412     11    414 2021-02-20 13:20:56 UTC+0000
 0xfffffa800513cb30:winlogon.exe                      508    412      5    120 2021-02-20 13:20:56 UTC+0000
 0xfffffa8003b10950:GoogleCrashHan                   3996   3896      5     95 2021-02-20 13:23:40 UTC+0000
 0xfffffa800288e060:GoogleCrashHan                   2960   3896      5    100 2021-02-20 13:23:40 UTC+0000
```

We see that `notepad.exe` is running and this might be a reference to the note mentioned in the description.

We then run the following command to dump the memory sections from PID `3800` which is `notepad.exe` from the output above:

`volatility -f memdump.mem --profile Win7SP1x64 memdump --dump-dir=./ -p 3800`

We then run the following command to get all the URLs from the memory dump since the organizers hinted that there is a URL to open:

`strings 3800.dmp | grep http > out.txt`

We see that there is one interesting URL: `https://imgur.com/a/pRWCNyo` which gives us the flag.

![flag](Images/flag.png)
