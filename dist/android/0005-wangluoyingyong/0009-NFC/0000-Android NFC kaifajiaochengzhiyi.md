Near Field Communication (NFC)为一短距离无线通信技术，通常有效通讯距离为4厘米以内。NFC工作频率为13.65兆赫兹，通信速率为106kbit/秒到848kbit/秒。
NFC通信总是由一个发起者(initiator)和一个接受者(target)组成。通常initiator 主动发送电磁场(RF)可以为被动式接受者(passive target)提供电源。其工作的基本原理和收音机类似。正是由于被动式接受者可以通过发起者提供电源，因此target 可以有非常简单的形式,比如标签，卡，sticker 的形式。
NFC 也支持点到点的通信(peer to peer) 此时参与通信的双方都有电源支持。
和其它无线通信方式如Bluetooth相比，NFC 支持的通信带宽和距离要小的多，但是它成本低，如价格标签可能只有几分钱，也不需要配对，搜寻设备等，通信双方可以在靠近的瞬间完成交互。
在Android NFC 应用中，Android手机通常是作为通信中的发起者，也就是作为NFC 的读写器。Android手机也可以模拟作为NFC通信的接受者且从Android 2.3.3起也支持P2P通信。
Android对NFC的支持主要在 android.nfc 和android.nfc.tech 两个包中。
android.nfc 包中主要类如下：
1、NfcManager 可以用来管理Android设备中指出的所有NFC Adapter，但由于大部分Android设备只支持一个NFC Adapter，可以直接使用getDefaultAapater 来获取系统支持的Adapter。
2、NfcAdapter为一NFC Adapter对象，可以用来定义一个Intent使系统在检测到NFC Tag时通知你定义的Activity，并提供用来注册forground tag 消息发送的方法等。
3、NdefMessage 和NdefRecord NDEF 为NFC forum 定义的数据格式。
4、Tag代表一个被动式Tag对象，可以代表一个标签，卡片，钥匙扣等。当Android设备检测到一个Tag时，会创建一个Tag对象，将其放在Intent对象，然后发送到相应的Activity。
android.nfc.tech 中则定义了可以对Tag进行的读写操作的类，这些类按照其使用的技术类型可以分成不同的类如：NfcA, NfcB, NfcF,以及MifareClassic 等。
常见的Tag为Mifare ，后面的例子将以这种Tag 为例介绍NFC读写方法。