---
description: شرح كيفيه انشاء كونفق اوبن كور لمعالجات انتل الجيل الرابع على الابتوبات وكافه الخيارات الضروريه.
---

# الجيل الرابع laptop
| الدعم | الاصدار |
| :--- | :--- |
| بداية الدعم على الماك | OS X 10.8, Mountain Lion |

إذا كنت تريد طريقه انشاء الكونفق يدويا ولك معرفه باللغه الانجليزي [هنا ](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/haswell.html)رابط يشرح كيفيه انشاء الكونفق للجيل الرابع لللابتوبات

**هذه الصفحة معدة على اساس انك تستخدم كونفقات هاكنتوش بالعربي**

???+ Warning "تنبية"
	قد تكون الصور احيانا غير محدثة او لاتظهر التعديلات الصحيحه
	**يرجى دائما اتباع الكلام المكتوب وتجاهل الصور** وظيفتها هي توضيح الاقسام لا غير الا لبعض الاستثانئات

## DeviceProperties

### Add

![](/img/config-setup/deviceproperties.png)

???+ info "PciRoot(0x0)/Pci(0x2,0x0)"
	هذا القسم مخصص لتحديد باتشات ال Framebuffer لكيكست [WhateverGreen](/EFI-setup/gathering-kexts#gpus)

	| AAPL,ig-platform-id | وصف |
	| :--- | :--- |
	| 0500260A | يستخدم عادة مع كروت HD5000,HD5100,HD5200 |
	| 0600260A | يستخدم عادة مع كروت HD4400,HD4600 |

	اعدادت اضافيه:

	| عنوان  | TYPE | القيمة | ملاحظة |
	| :--- | :--- | :--- | :--- |
	| framebuffer-patch-enable | Data | 01000000 | تفعيل باتشات |
	| framebuffer-cursormem | Data | 00009000 | تصحيح حجم الماوس لحل بعد القلتشات |
	
	اذا كان لديك كرت **HD4200,HD4400,HD4600** تحتاج الى اضافه:

	| عنوان  | النوع | القيمة | ملاحظة |
	| :--- | :--- | :--- | :--- |
	| device | Data | 12040000 | تغيير اسم الكرت حتى يتم دعمه |

## Kernel

### Quirks

![](/img/config-setup/kernel-quirks.png)

???+ info "Quirks"
	اعدادات مخصصه للكيرنل.
	
	| العنوان | مفعل | وصف |
	| :--- | :--- | :--- |
	| AppleXcpmCfgLock | True | غير ضروري اذا تم تعطيل `CFG-Lock` في البايوس |
	| DisableIOMapper | True | غير ضروري اذا تم تعطيل `VT-D` في البايوس |
	| LapicKernelPanic | False | اجهزه HP ستحتاج تفعيل هذا الاعداد |
	| PanicNoKextDump | True | |
	| PowerTimeoutKernelPanic | True | |
	| XhciPortLimit | True | |

## NVRAM

### Add

![](/img/config-setup/nvram-add.png)

???+ info "7C436110-AB2A-4BBB-A880-FE41995C9F82"

	هنا نقوم باضافه شروط اقلاع اضافيه مثلا لتحديد كروت الصوت وغيرها.
	 **اوامر اقلاع عامة:**

	| اوامر اقلاع  | وصف |
	| :--- | :--- |
	| **-v** |هذا احد اهم اوامر الاقلاع في عالم الهاكنتوش, يقوم بتفعيل وضع تفصيل حاله الاقلاع لاظهار جميع العمليات التي يقوم بها النظام. بدونه لايمكن معرفة سبب بانك بشكل صحيح فهو يظهر  معلومات مثلا حالة الاجهزه ايت مرحلة اقلاع فيها الجهاز وغيرها   |
	| **debug=0x100** | يقوم بتعطيل اعاده التشغيل في حالة حصول بانك في الاقلاع, لاعطائك فرصة لقرائة سبب توقف النظام **يستحسن استخدامة اثناء التثبيت وحل المشاكل** |
	| **keepsyms=1** | مرافق ل debug=0x100 بحيث يخبر النظام بطباعة علامات عند حصول بانك لاعطاء معلومات اكثر عن سبب حصول البانك بالاساس. |
	| **alcid=XX** | يستخدم لتحديد نوع كروت الصوت لتعريف [AppleALC.kext](/EFI-setup/gathering-kexts#sound) قائمة الكروت الصوت والرقم الخاص بها [من هنا](https://github.com/acidanthera/applealc/wiki/supported-codecs) **استخدم الارقام بعد كلمت layout** |

	* **اوامر اقلاع لكروت الشاشة**:

	| اوامر اقلاع | وصف |
	| :--- | :--- |
	| **agdpmod=pikera** | يستخدم لتعطيل board id على كروت navi(RX 5xxx) بدونه ستواجهه شاشة سوداء, لاتستخدمة على كروت من نوع اخر. |
	| **nvda_drv_vrl=1** | ضروري لتفعيل تعريفات انفيديا على فئة GTX 9xx و 10xx على سييرا وهاي سييرا |
	| **-wegnoegpu** | يستخدم لتعطيل جميع الكروت الخارجيه, مفيد بحاله اذا كان كرتك المنفصل غير مدعوم من النظام مثل كروت انفيديا الحديثة. **تمت اضافته سابقا**|

### Delete

**مخصص فقط للاجهزه التي لاتعمل فيها ال  ==NVRAM==**

- LegacyEnable: TRUE
	- يقوم بتخزين اعدادت ال nvram في ملف nvram.plist لمحاكاه دعم nvram
- LegacyOverwrite: TRUE
	- يسمح بتفعيل الاعدادات الموجوده في nvram.plist

## PlatformInfo

![](/img/config-setup/propertree-platforminfo.png)

هنا نقوم بوضع معلومات الجهاز مثل نوعه و السيريال ورقم اللوحة وغيرها, سنستخدم برنامج [GenSMBios](https://github.com/corpnewt/GenSMBIOS)

بعد تشغيل البرنامج اضغط رقم 1 لتنزيل الملفات الضرورية.

بعدها اضغط رقم 3 لتوليد معلومات الجهاز

بالنسبه للموديل الجهاز (SMBIOS) هناك عده اختيارات للجيل الرابع على الابتوبات:
igpu = كرت شاشه مدمج dgpu = كرت منفصل

| SMBIOS | نوع المعالج | نوع كرت الشاشة | حجم الشاشة |
| :--- | :--- | :--- | :--- |
| MacBookAir6,1 | ثنائي النواه 15w | iGPU: HD 5000 | 11" |
| MacBookAir6,2 | ثنائي النواه 15w | iGPU: HD 5000 | 13" |
| MacBookPro11,1 | ثنائي النواه 28w | iGPU: Iris 5100 | 13" |
| MacBookPro11,2 | رباعي النواه 45w | iGPU: Iris Pro 5200 | 15" |
| MacBookPro11,3 | رباعي النواه 45w | iGPU: Iris Pro 5200 + dGPU: GT750M | 15" |
| MacBookPro11,4 | رباعي النواه 45w | iGPU: Iris Pro 5200 | 15" |
| MacBookPro11,5 | رباعي النواه 45w | iGPU: Iris Pro 5200 + dGPU: R9 M370X | 15" |

![](/img/config-setup/gensmbios.png)

يتم نقل الارقام بالشكل الاتي للكونفق:

- `Type` يتم نسخه الى Generic -> SystemProductName.
- `Serial` يتم نسخه الى  Generic -> SystemSerialNumber.
- `Board Serial` يتم نسخه الى  Generic -> MLB.
- `SmUUID` يتم نسخه الى  Generic -> Generic -> SystemUUID.

## UEFI

### Quirks

???+ info "اذا كان لديك جهاز HP"
	يجب عليك تفعيل UnblockFsConnect
	
	| العنوان | مفعل | وصف |
	| :--- | :--- | :--- |
	| UnblockFsConnect | True | ضروري لمذربوردات HP |
