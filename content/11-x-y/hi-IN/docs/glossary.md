# शब्दावली

इस पेज पर उन कुछ शब्दों को परिभाषित किया गया है, जो इलेक्ट्रॉन के विकास में अक्सर इस्तेमाल किये गये हैं |

### ऐ एस ऐ आर

ऐ एस ऐ आर का मतलब है एटम शैल आर्काइव फॉर्मेट | एक [ऐ एस ऐ आर][asar] आर्काइव, `टार`-जैसा एक सरल फॉर्मेट है जो कि बहुत सारी फाइल्स को एक ही फाइल में जोड़ देता है | इससे इलेक्ट्रॉन बिना पूरी फाइल खोले किसी भी तरह से फाइल्स को पढ़ सकता है |

The ASAR format was created primarily to improve performance on Windows... TODO

### सीआरटी

सी रन टाइम लाइब्रेरी (सीआरटी), सी++ मानक लाइब्रेरी का एक भाग है जो आईएसओ सी99 मानक लाइब्रेरी को शामिल करती है | विसुअल सी++ लाइब्रेरीज जो सीआरटी का इस्तेमाल करती हैं, वे मूल कोड विकास का समर्थन करती हैं, और दोनों मिश्रित मूल और प्रबंधित कोड का; और शुद्ध प्रबंधित कोड का .एनईटी विकास के लिए |

### डीएमजी

एप्पल डिस्क इमेज एक पैकेजिंग फॉर्मेट है जो मैकओएस द्वारा इस्तेमाल किया जाता है | डीएमजी फाइल्स अक्सर एप्लीकेशन "इनस्टॉलर्स" का वितरण करने के लिए इस्तेमाल की जाती हैं | [इलेक्ट्रॉन -बिल्डर][], `डीएमजी` का एक निर्माण लक्ष्य के रूप में समर्थन करता है |

### आईएमई

इनपुट मेथड एडिटर | एक ऐसा प्रोग्राम, जो उपयोगकर्ताओं को उन करैक्टर्स और सिम्बल्स को इस्तेमाल करने की सुविधा देता है, जो कि उनके कीबोर्ड पर उपलब्ध नहीं होते | उदहारण के लिए, ये लैटिन कीबोर्ड के उपयोगकर्ताओं को चीनी, जापानी और इंडिक करैक्टरर्स को इस्तेमाल करने की सुविधा देता है |

### IDL

Interface description language. Write function signatures and data types in a format that can be used to generate interfaces in Java, C++, JavaScript, etc.

### आईपीसी

IPC stands for Inter-Process Communication. Electron uses IPC to send serialized JSON messages between the [main][] and [renderer][] processes.

### लिब क्रोमियम कंटेंट

एक साझा लाइब्रेरी जो [क्रोमियम कंटेंट मोड्यूल][] और उसके सभी आश्रितों (जैसे कि ब्लिंक, [वी8][] आदि) को समेटे हुए है | इसे "लिबसीसी" के नाम से भी जाना जाता है |

- [github.com/electron/libchromiumcontent](https://github.com/electron/libchromiumcontent)

### मुख्य प्रक्रिया

The main process, commonly a file named `main.js`, is the entry point to every Electron app. It controls the life of the app, from open to close. या मेन्यु, मेन्यु बार, डॉक, ट्रे, आदि जैसे मूल तत्वों को भी प्रबंधित करता है | The main process is responsible for creating each new renderer process in the app. The full Node API is built in.

Every app's main process file is specified in the `main` property in `package.json`. This is how `electron .` knows what file to execute at startup.

In Chromium, this process is referred to as the "browser process". It is renamed in Electron to avoid confusion with renderer processes.

इसे भी देखें: [प्रक्रिया](#process), [रेंदेरेर प्रक्रिया](#renderer-process)

### एमऐएस

Acronym for Apple's Mac App Store. For details on submitting your app to the MAS, see the [Mac App Store Submission Guide][].

### Mojo

An IPC system for communicating intra- or inter-process, and that's important because Chrome is keen on being able to split its work into separate processes or not, depending on memory pressures etc.

See https://chromium.googlesource.com/chromium/src/+/master/mojo/README.md

### मूल मोडयुल्स

Native modules (also called [addons][] in Node.js) are modules written in C or C++ that can be loaded into Node.js or Electron using the require() function, and used as if they were an ordinary Node.js module. वे मुख्यतः नोड.जेएस और सी/सी++ लाइब्रेरीज में चल रही जावास्क्रिप्ट को इंटरफ़ेस प्रदान करने के लिए इस्तेमाल किये जाते हैं |

मूल नोड मोडयुल्स इलेक्ट्रॉन द्वारा समर्थित हैं, पर चूँकि इस बात की काफी सम्भावना है कि इलेक्ट्रॉन आपके सिस्टम में इन्स्टाल नोड बाइनरी से अलग वी8 संस्करण इस्तेमाल करता हो; इसलिए मूल मोडयुल्स का निर्माण करते वक़्त आपको मैन्युअली ही इलेक्ट्रॉन के हेडर्स की लोकेशन निर्दिष्ट करनी होगी |

इसे भी देखें: [मूल नोड मोडयुल्स का इस्तेमाल करना][] |

### एनएसआईएस

नल्लसॉफ्ट स्क्रिप्टएबल इनस्टॉल सिस्टम, माइक्रोसॉफ्ट विंडोज के लिए एक स्क्रिप्ट-संचालित इंस्टालर ऑथरिंग टूल है | इसे मुफ़्त सॉफ्टवेर लाइसेंस के एक संयोजन के रूप में जारी किया गया है, और यह इनस्टॉलशील्ड जैसे व्यावसायिक उत्पादों के विकल्प में व्यापक रूप से इस्तेमाल किया जाता है | [इलेक्ट्रॉन -बिल्डर][], एनएसआईएस का एक निर्माण लक्ष्य के रूप में समर्थन करता है |

### ओएसआर

OSR (Off-screen rendering) can be used for loading heavy page in background and then displaying it after (it will be much faster). It allows you to render page without showing it on screen.

### प्रक्रिया

प्रक्रिया, कंप्यूटर प्रोग्राम का वह रूप है जो उस समय चल रहा होता है | इलेक्ट्रॉन एप्प्स जो [मुख्य][] और एक या कई [रेंदेरेर][] प्रक्रियाओं का इस्तेमाल करती हैं, वे असल में बहुत सारे प्रोग्रम्म्स को एक साथ चला रही होती हैं |

नोड.जेएस और इलेक्ट्रॉन में, हर चलती प्रक्रिया के पास एक `प्रोसेस` ऑब्जेक्ट होता है | यह ऑब्जेक्ट एक वैश्विक होता है, और यह वर्तमान प्रोसेस की जानकारी और उस पर नियंत्रण देता है | चूँकि यह एक वैश्विक है, इसलिए यह सभी एप्लीकेशनस को बिना रेकुआयर() के उपलब्ध होता है |

इसे भी देखें: [मुख्य प्रक्रिया](#main-process), [रेंदेरेर प्रक्रिया](#renderer-process)

### रेंदेरेर प्रक्रिया

The renderer process is a browser window in your app. Unlike the main process, there can be multiple of these and each is run in a separate process. They can also be hidden.

सामान्य ब्राउज़र्स में, वेब पेजेस अक्सर सैंडबॉक्स वातावरण में चलते हैं और इन्हें मूल संसाधनों तक पहुँच उपलब्ध नहीं होती | पर इलेक्ट्रॉन उपयोगकर्ताओं के पास वेब पेजेज में नोड.जेएस का इस्तेमाल करने की शक्ति होती है, जिससे कि वे ऑपरेटिंग सिस्टम के निचले स्तर की इंटरेक्शन कर सकते हैं |

इसे भी देखें: [प्रक्रिया](#process), [मुख्य प्रक्रिया](#main-process)

### स्कुइर्रेल

स्कुइर्रेल एक मुक्त स्त्रोत ढांचा है, जो इलेक्ट्रॉन एप्प्स को नये संस्करण आने पर स्वतः ही अपडेट होने की क्षमता प्रदान करता है | स्कुइर्रेल के साथ शुरुआत करने की जानकारी पाने के लिए, [स्वतः अपडेटर][] ऐपीआई देखें |

### यूजरलैंड

यह शब्द यूनिक्स समुदाय में उत्पन्न हुआ था, जहाँ "यूजरलैंड" या "यूजरस्पेस" उन प्रोग्रम्म्स को कहते थे जो ऑपरेटिंग सिस्टम कर्नेल के बाहर चलते थे | बड़े "यूजर" समुदाय द्वारा हाल ही में, यह शब्द "नोड कोर" और एनपीएम रजिस्ट्री में प्रकाशित पैकेजेस में मौज़ूद सुविधाओं के बीच अंतर करने के लिए नोड और एनपीएम समुदायों में काफी मशहूर किया गया है |

नोड ही की तरह, इलेक्ट्रॉन का ध्यान भी ऐपीआई का एक छोटा सा सेट रखने पर केन्द्रित है जो बहु-प्लेटफार्म डेस्कटॉप एप्लीकेशनस का विकास करने के लिए सभी ज़रूरी प्रिमिटिव्स प्रदान कर सके | यही डिजाईन सोच इलेक्ट्रॉन को बहुत ज्यादा नियमों और इस्तेमाल करने के बारे में बताने की बजाये, एक लचीला औज़ार बने रहने में मदद करती है | यूजरलैंड उपयोगकर्ताओं को उन औजारों को बनाने और साझा करने की सुविधा प्रदान करता है जो "कोर" में मौज़ूद चीज़ों के साथ-साथ दूसरी कार्यक्षमता भी प्रदान करते हों |

### वी8

V8 is Google's open source JavaScript engine. It is written in C++ and is used in Google Chrome. V8 can run standalone, or can be embedded into any C++ application.

इलेक्ट्रॉन वी8 का निर्माण क्रोमियम के हिस्से के रूप में करता है और फ़िर निर्माण करने के दौरान नोड का उस वी8 की तरफ इशारा कर देता है |

V8's version numbers always correspond to those of Google Chrome. Chrome 59 includes V8 5.9, Chrome 58 includes V8 5.8, etc.

- [developers.google.com/v8](https://developers.google.com/v8)
- [nodejs.org/api/v8.html](https://nodejs.org/api/v8.html)
- [docs/development/v8-development.md](development/v8-development.md)

### वेबव्यू

`webview` tags are used to embed 'guest' content (such as external web pages) in your Electron app. They are similar to `iframe`s, but differ in that each webview runs in a separate process. इसके पास आपके वेब पेज जैसी अनुमतियाँ नहीं होती हैं और आपकी एप्प और शामिल सामग्री के बीच सभी इंटरेक्शन बिना ताल-मेल के होगी | इससे आपकी एप्प, शामिल सामग्री से सुरक्षित रहती है |

[addons]: https://nodejs.org/api/addons.html
[asar]: https://github.com/electron/asar
[स्वतः अपडेटर]: api/auto-updater.md
[क्रोमियम कंटेंट मोड्यूल]: https://www.chromium.org/developers/content-module
[इलेक्ट्रॉन -बिल्डर]: https://github.com/electron-userland/electron-builder
[Mac App Store Submission Guide]: tutorial/mac-app-store-submission-guide.md
[main]: #main-process
[मुख्य]: #main-process
[renderer]: #renderer-process
[रेंदेरेर]: #renderer-process
[मूल नोड मोडयुल्स का इस्तेमाल करना]: tutorial/using-native-node-modules.md
[वी8]: #v8
