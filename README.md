# NetMap Android 1.1.0

Android-застосунок мережевого сканування на Kotlin + Jetpack Compose.

## Функції
- автоматичне визначення IPv4, шлюзу та CIDR активної мережі;
- ручне введення IPv4 CIDR та пошук доступних вузлів;
- best-effort визначення hostname;
- SNMP v2c GET: `sysName`, `sysDescr`, `sysServices`;
- LLDP-MIB walk: chassis ID, port ID, port description, remote system name;
- BRIDGE-MIB FDB walk (`dot1dTpFdbPort`) з перетворенням bridge port → ifIndex → ifName;
- розгортання картки вузла для перегляду SNMP, LLDP та FDB;
- спрощена L3-карта вузлів та шлюзу;
- експорт результатів, включно з SNMP/LLDP/FDB, у JSON;
- GitHub Actions для автоматичного складання debug APK.

## Важливі обмеження
- SNMP-опитування виконується для вузлів, які знайшов L3 discovery. Пристрої, що блокують ICMP/`isReachable`, можуть не потрапити до списку, навіть якщо UDP/161 доступний.
- SNMP v2c не шифрує community. Задайте read-only community, обмежте доступ до UDP/161 ACL-правилами та використовуйте застосунок у довіреній мережі. Не використовуйте приватну community з правами запису.
- LLDP залежить від увімкненого LLDP та реалізації LLDP-MIB на пристрої.
- FDB реалізовано через стандартний BRIDGE-MIB (`dot1dTpFdbPort`). VLAN-aware/Q-BRIDGE-MIB таблиці окремо не опитуються; у VLAN-мережах результат може бути неповним.
- LLDP та FDB відображаються як дані окремого SNMP-вузла. Це ще не повноцінний автоматичний граф фізичних з'єднань порт-до-порту.
- Android обмежує доступ до ARP/neighbor table; MAC-адреси хостів можуть бути недоступні. `isReachable()` залежить від прошивки, мережі та ICMP-фільтрації.
- Підмережі обмежені IPv4 /16…/30, максимум 1022 адреси за один запуск.

## Збірка
Відкрийте папку `NetMapAndroid` в Android Studio та дочекайтеся Gradle Sync. Потрібні JDK 17, Android SDK 35 та Gradle 8.9 (AGP 8.7.3). Для локальної збірки: `gradle assembleDebug`.

## APK через GitHub Actions
Push у `main`/`master` запускає workflow автоматично. Також можна відкрити **Actions → Build NetMap APK → Run workflow**. Після успішного завершення завантажте artifact **NetMap-debug-apk**; APK буде `app-debug.apk`.

Це debug APK, не підписаний production/release build. Для release потрібні власний keystore та налаштування підпису.
