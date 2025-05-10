Albatta, quyida sayohatlarni bron qilish mobil ilovasi uchun MVP (Minimal Hayotiy Mahsulot) bosqichi uchun batafsil Dasturiy Ta'minot Talablari Spetsifikatsiyasi (SRS) hujjatini taqdim etaman.

---

**Dasturiy Ta'minot Talablari Spetsifikatsiyasi (SRS)**

**Loyiha: Mobil Sayohat Bron Qilish Ilovasi (MVP)**

**Versiya:** 1.0
**Sana:** 2023-10-27

---

**Mundarija:**

1.  📄 Loyiha Haqida Umumiy Ma'lumot
2.  👥 Foydalanuvchi Rollari
3.  🎒 Tur Paketlar Tizimi
4.  🧾 Bron Qilish Tizimi
5.  🌟 Baholash va Sharhlar Tizimi
6.  💬 Xabar Almashish Tizimi
7.  🔔 Bildirishnomalar Tizimi
8.  📊 Boshqaruv Paneli va Statistikalar (Admin va Agent uchun)
9.  🔐 Autentifikatsiya va Xavfsizlik
10. 🖼️ UX Tavsifi
11. 🧱 Texnologik Stek Tavsiyasi

---

### 1. 📄 Loyiha Haqida Umumiy Ma'lumot

**Ilova maqsadi va vazifasi:**
Ilovaning asosiy maqsadi – sayohatchilar uchun tur paketlarni topish, ko'rib chiqish va bron qilish jarayonini soddalashtirish, tur agentlari uchun o'z xizmatlarini kengroq auditoriyaga taklif qilish imkoniyatini yaratish, hamda administratorlar uchun platformani samarali boshqarish vositalarini taqdim etish.

**Maqsadli foydalanuvchilar:**
*   **Sayohatchi (Foydalanuvchi):** Turli yo'nalishlar va tur paketlarni qidirayotgan, oson bron qilish va ishonchli ma'lumot olishni istagan shaxslar.
*   **Tur Agent:** O'z tur paketlarini platformada joylashtirib, mijozlar oqimini oshirishni maqsad qilgan sayyohlik agentliklari yoki individual agentlar.
*   **Admin:** Platformaning umumiy faoliyatini nazorat qiluvchi, foydalanuvchilarni boshqaruvchi va tizim sozlamalarini amalga oshiruvchi shaxs.

**MVP doirasi va startap mulohazalari:**
MVP (Minimal Hayotiy Mahsulot) bosqichida asosiy e'tibor quyidagi funksiyalarga qaratiladi:
*   Foydalanuvchilarni ro'yxatdan o'tkazish va autentifikatsiya qilish.
*   Tur agentlari tomonidan tur paketlarni yaratish va boshqarish.
*   Sayohatchilar tomonidan tur paketlarni qidirish, ko'rish va bron qilish.
*   Oddiy xabar almashish tizimi (sayohatchi va agent o'rtasida).
*   Baholash va sharhlar tizimi.
*   Admin uchun asosiy boshqaruv paneli.

Startap sifatida asosiy mulohazalar – tezkor bozorga chiqish, foydalanuvchilarning dastlabki fikr-mulohazalarini yig'ish va kelgusi rivojlanish yo'nalishlarini aniqlashdan iborat. Minimal byudjet va resurslar bilan maksimal qiymat yaratishga harakat qilinadi.

### 2. 👥 Foydalanuvchi Rollari

**Foydalanuvchi rollari ro'yxati va tushuntirish:**

*   **Foydalanuvchi (Traveler):** Ilovaning asosiy iste'molchisi. Tur paketlarni qidiradi, ko'radi, bron qiladi, agentlar bilan muloqot qiladi va sayohatlar haqida sharhlar qoldiradi.
*   **Tur Agent (Tour Agent):** Tur paketlarni yaratadi, tahrirlaydi, o'chiradi, bronlarni boshqaradi, sayohatchilar bilan muloqot qiladi va o'z faoliyati bo'yicha statistikani ko'radi.
*   **Admin (Admin):** Butun platformani boshqaradi. Foydalanuvchilar va agentlarni boshqaradi, tur paketlarni tasdiqlaydi yoki rad etadi, tizim sozlamalarini o'zgartiradi, umumiy statistikani kuzatadi va munozarali masalalarni hal qiladi.

**Har bir foydalanuvchi turi uchun Dart class sintaksisidagi to'liq ma'lumotlar modeli:**

```dart
import 'package:flutter/foundation.dart';

enum UserRole { traveler, tourAgent, admin }

@immutable
class UserModel {
  final String userId;
  final String email;
  final String fullName;
  final String? phoneNumber;
  final String? profileImageUrl;
  final UserRole role;
  final DateTime createdAt;
  final DateTime updatedAt;

  // Agentga xos maydonlar (agar role == UserRole.tourAgent bo'lsa)
  final String? agencyName;
  final String? licenseNumber; // Litsenziya raqami
  final List<String>? managedPackageIds; // Agent boshqaradigan paketlar ID'lari

  // Admin uchun qo'shimcha maxsus maydonlar bo'lishi mumkin, ammo MVP uchun kerak emas

  const UserModel({
    required this.userId,
    required this.email,
    required this.fullName,
    this.phoneNumber,
    this.profileImageUrl,
    required this.role,
    required this.createdAt,
    required this.updatedAt,
    this.agencyName,
    this.licenseNumber,
    this.managedPackageIds,
  });

  // Nusxalash, JSON konvertatsiyasi va boshqa yordamchi metodlar
  // (copyWith, toJson, fromJson, toString, hashCode, ==)
  // Bularni generatsiya qilish uchun freezed yoki json_serializable ishlatish tavsiya etiladi.
  // Quyida namunaviy fromJson va toJson:

  factory UserModel.fromJson(Map<String, dynamic> json) {
    return UserModel(
      userId: json['userId'] as String,
      email: json['email'] as String,
      fullName: json['fullName'] as String,
      phoneNumber: json['phoneNumber'] as String?,
      profileImageUrl: json['profileImageUrl'] as String?,
      role: UserRole.values.firstWhere((e) => e.toString() == json['role']),
      createdAt: DateTime.parse(json['createdAt'] as String),
      updatedAt: DateTime.parse(json['updatedAt'] as String),
      agencyName: json['agencyName'] as String?,
      licenseNumber: json['licenseNumber'] as String?,
      managedPackageIds: (json['managedPackageIds'] as List<dynamic>?)
          ?.map((id) => id as String)
          .toList(),
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'userId': userId,
      'email': email,
      'fullName': fullName,
      'phoneNumber': phoneNumber,
      'profileImageUrl': profileImageUrl,
      'role': role.toString(),
      'createdAt': createdAt.toIso8601String(),
      'updatedAt': updatedAt.toIso8601String(),
      'agencyName': agencyName,
      'licenseNumber': licenseNumber,
      'managedPackageIds': managedPackageIds,
    };
  }
}
```

**Har bir rolning asosiy xususiyatlari va ruxsatlari:**

*   **Foydalanuvchi (Traveler):**
    *   Ro'yxatdan o'tish/Tizimga kirish
    *   Profilni tahrirlash (ism, rasm, telefon)
    *   Tur paketlarni qidirish va filtrlash
    *   Tur paket tafsilotlarini ko'rish
    *   Tur paketlarni bron qilish
    *   O'z bronlarini ko'rish va boshqarish (MVPda bekor qilish cheklangan bo'lishi mumkin)
    *   Tur agentlari bilan xabar almashish
    *   Yakunlangan turlar uchun baho berish va sharh qoldirish
    *   Bildirishnomalarni qabul qilish

*   **Tur Agent (Tour Agent):**
    *   Ro'yxatdan o'tish/Tizimga kirish (Admin tomonidan tasdiqlanishi mumkin)
    *   Profilni tahrirlash (agentlik nomi, litsenziya, aloqa ma'lumotlari)
    *   Tur paketlarni yaratish, ko'rish, tahrirlash, o'chirish (CRUD)
    *   O'z paketlariga kelib tushgan bronlarni ko'rish va boshqarish (tasdiqlash, rad etish)
    *   Sayohatchilar bilan xabar almashish
    *   O'z faoliyati bo'yicha statistikani ko'rish (sotuvlar, reytinglar)
    *   Bildirishnomalarni qabul qilish

*   **Admin (Admin):**
    *   Tizimga kirish
    *   Barcha foydalanuvchilar (Sayohatchilar va Agentlar) ro'yxatini ko'rish va boshqarish (bloklash, o'chirish, rollarni o'zgartirish)
    *   Tur agentlarining ro'yxatdan o'tish so'rovlarini tasdiqlash/rad etish
    *   Barcha tur paketlarni ko'rish va boshqarish (tahrirlash, o'chirish, tasdiqlash)
    *   Barcha bronlarni ko'rish
    *   Baholash va sharhlarni moderatsiya qilish (o'chirish)
    *   Umumiy platforma statistikasini ko'rish
    *   Bildirishnomalarni yuborish (masalan, tizim yangilanishlari haqida)

### 3. 🎒 Tur Paketlar Tizimi

**Tur paketlarning maqsadi:**
Tur paketlar – bu tur agentlari tomonidan taklif qilinadigan, oldindan belgilangan marshrut, xizmatlar to'plami (turar joy, transport, ekskursiyalar va h.k.) va narxga ega bo'lgan sayohat takliflaridir. Ular sayohatchilarga qulay va tartibli sayohatni rejalashtirish imkonini beradi.

**Barcha maydonlar va ma'lumotlar turlari bilan to'liq Dart modeli (`TourPackage`):**

```dart
import 'package:flutter/foundation.dart';

enum TourCategory { adventure, cultural, beach, cityBreak, nature, other }

@immutable
class TourPackage {
  final String packageId;
  final String agentId; // Ushbu paketni yaratgan agentning ID'si
  final String title;
  final String description;
  final String destinationCity; // Manzil (shahar, mamlakat)
  final String country;
  final int durationDays; // Davomiyligi (kunlarda)
  final double price;
  final String currency; // Masalan, "UZS", "USD"
  final List<String> includedServices; // "Nonushta", "Transfer", "Gid xizmati"
  final List<String> notIncludedServices;
  final List<String> imageUrls; // Paket rasmlari URL manzillari
  final int availableSlots; // Mavjud o'rinlar soni
  final DateTime startDate; // Boshlanish sanasi
  final DateTime? endDate; // Tugash sanasi (davomiylikka qarab hisoblanishi mumkin)
  final TourCategory category;
  final double averageRating; // O'rtacha baho (0.0 - 5.0)
  final int totalReviews;
  final bool isApprovedByAdmin; // Admin tomonidan tasdiqlanganmi
  final DateTime createdAt;
  final DateTime updatedAt;

  const TourPackage({
    required this.packageId,
    required this.agentId,
    required this.title,
    required this.description,
    required this.destinationCity,
    required this.country,
    required this.durationDays,
    required this.price,
    required this.currency,
    this.includedServices = const [],
    this.notIncludedServices = const [],
    this.imageUrls = const [],
    required this.availableSlots,
    required this.startDate,
    this.endDate,
    required this.category,
    this.averageRating = 0.0,
    this.totalReviews = 0,
    this.isApprovedByAdmin = false, // Dastlab tasdiqlanmagan
    required this.createdAt,
    required this.updatedAt,
  });

  factory TourPackage.fromJson(Map<String, dynamic> json) {
    return TourPackage(
      packageId: json['packageId'] as String,
      agentId: json['agentId'] as String,
      title: json['title'] as String,
      description: json['description'] as String,
      destinationCity: json['destinationCity'] as String,
      country: json['country'] as String,
      durationDays: json['durationDays'] as int,
      price: (json['price'] as num).toDouble(),
      currency: json['currency'] as String,
      includedServices: (json['includedServices'] as List<dynamic>)
          .map((service) => service as String)
          .toList(),
      notIncludedServices: (json['notIncludedServices'] as List<dynamic>)
          .map((service) => service as String)
          .toList(),
      imageUrls: (json['imageUrls'] as List<dynamic>)
          .map((url) => url as String)
          .toList(),
      availableSlots: json['availableSlots'] as int,
      startDate: DateTime.parse(json['startDate'] as String),
      endDate: json['endDate'] != null ? DateTime.parse(json['endDate'] as String) : null,
      category: TourCategory.values.firstWhere((e) => e.toString() == json['category']),
      averageRating: (json['averageRating'] as num?)?.toDouble() ?? 0.0,
      totalReviews: json['totalReviews'] as int? ?? 0,
      isApprovedByAdmin: json['isApprovedByAdmin'] as bool? ?? false,
      createdAt: DateTime.parse(json['createdAt'] as String),
      updatedAt: DateTime.parse(json['updatedAt'] as String),
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'packageId': packageId,
      'agentId': agentId,
      'title': title,
      'description': description,
      'destinationCity': destinationCity,
      'country': country,
      'durationDays': durationDays,
      'price': price,
      'currency': currency,
      'includedServices': includedServices,
      'notIncludedServices': notIncludedServices,
      'imageUrls': imageUrls,
      'availableSlots': availableSlots,
      'startDate': startDate.toIso8601String(),
      'endDate': endDate?.toIso8601String(),
      'category': category.toString(),
      'averageRating': averageRating,
      'totalReviews': totalReviews,
      'isApprovedByAdmin': isApprovedByAdmin,
      'createdAt': createdAt.toIso8601String(),
      'updatedAt': updatedAt.toIso8601String(),
    };
  }
}
```

**Foydalanuvchilar va agentlar ushbu ma'lumotlar bilan qanday amallarni bajarishi:**
*   **Foydalanuvchi (Traveler):**
    *   Tur paketlarni ko'rish (Read)
    *   Qidirish va filtrlash (Read)
*   **Tur Agent (Tour Agent):**
    *   Tur paketlarni yaratish (Create)
    *   O'ziga tegishli tur paketlarni ko'rish (Read)
    *   O'ziga tegishli tur paketlarni tahrirlash (Update)
    *   O'ziga tegishli tur paketlarni o'chirish (Delete)
    *   (Yaratilgan paketlar Admin tomonidan tasdiqlanishi kerak bo'lishi mumkin)
*   **Admin (Admin):**
    *   Barcha tur paketlarni ko'rish (Read)
    *   Tur paketlarni tahrirlash (Update) (Masalan, xatoliklarni tuzatish)
    *   Tur paketlarni o'chirish (Delete)
    *   Tur paketlarni tasdiqlash/rad etish (Approve/Reject)

### 4. 🧾 Bron Qilish Tizimi

**Bron qilishning maqsadi:**
Bron qilish tizimi sayohatchilarga tanlangan tur paket uchun o'rinlarni band qilish va sayohatni rasmiylashtirish imkonini beradi. Shuningdek, u agentlarga o'z xizmatlariga bo'lgan talabni kuzatish va boshqarishda yordam beradi.

**Barcha kerakli maydonlar bilan Dart modeli (`Booking`):**
```dart
import 'package:flutter/foundation.dart';

enum BookingStatus {
  pending, // Kutmoqda (to'lovni yoki agent tasdig'ini)
  confirmed, // Tasdiqlangan
  cancelledByTraveler, // Sayohatchi tomonidan bekor qilingan
  cancelledByAgent, // Agent tomonidan bekor qilingan
  completed, // Yakunlangan (sayohat tugagan)
  rejected // Rad etilgan (masalan, o'rin yo'q)
}

enum PaymentStatus {
  pending, // To'lov kutilyapti
  paid, // To'langan
  failed, // To'lov amalga oshmadi
  refunded, // Qaytarilgan
  partiallyPaid // Qisman to'langan (MVP uchun emas)
}

@immutable
class Booking {
  final String bookingId;
  final String packageId;
  final String travelerId; // Bron qilgan sayohatchining ID'si
  final String agentId; // Paket egasi bo'lgan agentning ID'si
  final DateTime bookingDate; // Bron qilingan sana
  final int numberOfTravelers; // Sayohatchilar soni
  final double totalPrice; // Umumiy narx
  final String currency;
  final BookingStatus bookingStatus;
  final PaymentStatus paymentStatus;
  final String? specialRequests; // Maxsus talablar
  final String? paymentTransactionId; // To'lov tranzaksiyasi ID'si (agar mavjud bo'lsa)
  final DateTime createdAt;
  final DateTime updatedAt;

  const Booking({
    required this.bookingId,
    required this.packageId,
    required this.travelerId,
    required this.agentId,
    required this.bookingDate,
    required this.numberOfTravelers,
    required this.totalPrice,
    required this.currency,
    this.bookingStatus = BookingStatus.pending,
    this.paymentStatus = PaymentStatus.pending,
    this.specialRequests,
    this.paymentTransactionId,
    required this.createdAt,
    required this.updatedAt,
  });

  factory Booking.fromJson(Map<String, dynamic> json) {
    return Booking(
      bookingId: json['bookingId'] as String,
      packageId: json['packageId'] as String,
      travelerId: json['travelerId'] as String,
      agentId: json['agentId'] as String,
      bookingDate: DateTime.parse(json['bookingDate'] as String),
      numberOfTravelers: json['numberOfTravelers'] as int,
      totalPrice: (json['totalPrice'] as num).toDouble(),
      currency: json['currency'] as String,
      bookingStatus: BookingStatus.values.firstWhere((e) => e.toString() == json['bookingStatus']),
      paymentStatus: PaymentStatus.values.firstWhere((e) => e.toString() == json['paymentStatus']),
      specialRequests: json['specialRequests'] as String?,
      paymentTransactionId: json['paymentTransactionId'] as String?,
      createdAt: DateTime.parse(json['createdAt'] as String),
      updatedAt: DateTime.parse(json['updatedAt'] as String),
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'bookingId': bookingId,
      'packageId': packageId,
      'travelerId': travelerId,
      'agentId': agentId,
      'bookingDate': bookingDate.toIso8601String(),
      'numberOfTravelers': numberOfTravelers,
      'totalPrice': totalPrice,
      'currency': currency,
      'bookingStatus': bookingStatus.toString(),
      'paymentStatus': paymentStatus.toString(),
      'specialRequests': specialRequests,
      'paymentTransactionId': paymentTransactionId,
      'createdAt': createdAt.toIso8601String(),
      'updatedAt': updatedAt.toIso8601String(),
    };
  }
}
```

**Kim qanday amallarni bajarishi:**
*   **Foydalanuvchi (Traveler):**
    *   Bron yaratish (Create) (To'lov jarayoni bilan birga)
    *   O'z bronlarini ko'rish (Read)
    *   Bronni bekor qilish (Update status to `cancelledByTraveler`) (Bekor qilish siyosatiga bog'liq)
*   **Tur Agent (Tour Agent):**
    *   O'z paketlariga oid bronlarni ko'rish (Read)
    *   Bronni tasdiqlash (Update status to `confirmed`)
    *   Bronni rad etish (Update status to `rejected`)
    *   Bronni bekor qilish (Update status to `cancelledByAgent`) (Agar kerak bo'lsa)
*   **Admin (Admin):**
    *   Barcha bronlarni ko'rish (Read)
    *   Munozarali bronlarni boshqarish (Update)

### 5. 🌟 Baholash va Sharhlar Tizimi

**Sharhlar tizimining maqsadi:**
Sayohatchilarga o'z tajribalari (tur paket va agent xizmati) haqida fikr bildirish imkonini berish. Bu boshqa foydalanuvchilarga qaror qabul qilishda yordam beradi va agentlarga xizmat sifatini yaxshilash uchun qayta aloqa taqdim etadi.

**Dart modeli (`Review`):**
```dart
import 'package:flutter/foundation.dart';

@immutable
class Review {
  final String reviewId;
  final String packageId; // Qaysi paketga sharh yozilgan
  final String travelerId; // Kim sharh yozgan
  final String agentId; // Qaysi agentning paketiga sharh yozilgan
  final int rating; // Baho (1 dan 5 gacha)
  final String? comment; // Sharh matni
  final DateTime reviewDate;
  final DateTime createdAt;
  final DateTime updatedAt;

  const Review({
    required this.reviewId,
    required this.packageId,
    required this.travelerId,
    required this.agentId,
    required this.rating,
    this.comment,
    required this.reviewDate,
    required this.createdAt,
    required this.updatedAt,
  });

  factory Review.fromJson(Map<String, dynamic> json) {
    return Review(
      reviewId: json['reviewId'] as String,
      packageId: json['packageId'] as String,
      travelerId: json['travelerId'] as String,
      agentId: json['agentId'] as String,
      rating: json['rating'] as int,
      comment: json['comment'] as String?,
      reviewDate: DateTime.parse(json['reviewDate'] as String),
      createdAt: DateTime.parse(json['createdAt'] as String),
      updatedAt: DateTime.parse(json['updatedAt'] as String),
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'reviewId': reviewId,
      'packageId': packageId,
      'travelerId': travelerId,
      'agentId': agentId,
      'rating': rating,
      'comment': comment,
      'reviewDate': reviewDate.toIso8601String(),
      'createdAt': createdAt.toIso8601String(),
      'updatedAt': updatedAt.toIso8601String(),
    };
  }
}
```

**Kim joylashtirishi, tahrirlashi va o'chirishi mumkin:**
*   **Foydalanuvchi (Traveler):**
    *   Faqatgina bron qilingan va yakunlangan tur paket uchun sharh qoldirishi mumkin (Post).
    *   O'z sharhini ma'lum vaqt ichida tahrirlashi mumkin (Update) (MVP uchun bu funksiya qoldirilishi mumkin).
    *   O'z sharhini o'chirishi mumkin (Delete) (MVP uchun bu funksiya qoldirilishi yoki faqat Admin uchun bo'lishi mumkin).
*   **Tur Agent (Tour Agent):**
    *   O'z paketlariga yozilgan sharhlarni ko'rishi mumkin (Read).
    *   Sharhlarga javob yozishi mumkin (MVP dan keyingi bosqichda).
*   **Admin (Admin):**
    *   Barcha sharhlarni ko'rishi mumkin (Read).
    *   Qoidalarga zid bo'lgan sharhlarni o'chirishi mumkin (Delete/Moderate).

### 6. 💬 Xabar Almashish Tizimi

**Foydalanuvchi va Agent o'rtasidagi oddiy so'rov yoki chat tizimi:**
Sayohatchilar tur paketlar haqida qo'shimcha ma'lumot olish yoki maxsus so'rovlar yuborish uchun tur agentlari bilan bevosita muloqot qilishlari uchun oddiy chat tizimi. Har bir chat sayohatchi va agent o'rtasidagi individual suhbat bo'ladi.

**Dart modeli (`Message`):**
```dart
import 'package:flutter/foundation.dart';

@immutable
class Message {
  final String messageId;
  final String chatId; // Sayohatchi va Agent o'rtasidagi suhbat IDsi (masalan, travelerId_agentId_packageId)
  final String senderId; // Xabarni yuboruvchi ID'si
  final String receiverId; // Xabarni qabul qiluvchi ID'si
  final String textContent; // Xabar matni
  final DateTime timestamp; // Yuborilgan vaqti
  final bool isRead; // O'qilganmi

  const Message({
    required this.messageId,
    required this.chatId,
    required this.senderId,
    required this.receiverId,
    required this.textContent,
    required this.timestamp,
    this.isRead = false,
  });

   factory Message.fromJson(Map<String, dynamic> json) {
    return Message(
      messageId: json['messageId'] as String,
      chatId: json['chatId'] as String,
      senderId: json['senderId'] as String,
      receiverId: json['receiverId'] as String,
      textContent: json['textContent'] as String,
      timestamp: DateTime.parse(json['timestamp'] as String),
      isRead: json['isRead'] as bool? ?? false,
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'messageId': messageId,
      'chatId': chatId,
      'senderId': senderId,
      'receiverId': receiverId,
      'textContent': textContent,
      'timestamp': timestamp.toIso8601String(),
      'isRead': isRead,
    };
  }
}

// Chat ro'yxati uchun qo'shimcha model bo'lishi mumkin
@immutable
class ChatThread {
  final String chatId;
  final String travelerId;
  final String agentId;
  final String? packageId; // Qaysi paket bo'yicha suhbat (ixtiyoriy)
  final String lastMessage;
  final DateTime lastMessageTimestamp;
  final int unreadCount; // O'qilmagan xabarlar soni

  const ChatThread({
    required this.chatId,
    required this.travelerId,
    required this.agentId,
    this.packageId,
    required this.lastMessage,
    required this.lastMessageTimestamp,
    required this.unreadCount,
  });
}

```

**Xususiyatlar:**
*   Xabarlar real vaqtda (yoki yaqin real vaqtda) yetkaziladi.
*   O'qilmagan xabarlar belgilanadi.
*   Suhbatlar ro'yxati va individual suhbat oynasi.

### 7. 🔔 Bildirishnomalar Tizimi

**Push bildirishnomalari tushuntirish:**
Push bildirishnomalari foydalanuvchilarni ilova ichidagi muhim voqealar (yangi xabar, bron statusining o'zgarishi, yangi takliflar) haqida ilova yopiq bo'lsa ham xabardor qilish uchun ishlatiladi. Bu foydalanuvchilarning jalb qilinishini oshiradi.

**Dart modeli (`AppNotification`):**
```dart
import 'package:flutter/foundation.dart';

enum AppNotificationType {
  newBooking, // Agent uchun yangi bron
  bookingStatusChanged, // Foydalanuvchi va Agent uchun bron statusi o'zgardi
  newMessage, // Yangi xabar keldi
  packageApproved, // Agent uchun paket tasdiqlandi
  packageRejected, // Agent uchun paket rad etildi
  reviewReceived, // Agent uchun yangi sharh
  generalAdminMessage, // Umumiy admin xabari
  // Boshqa turlar...
}

@immutable
class AppNotification {
  final String notificationId;
  final String recipientUserId; // Bildirishnoma kimga yuborilgan (Traveler, Agent yoki Admin ID'si)
  final AppNotificationType type;
  final String title;
  final String body;
  final Map<String, String>? data; // Qo'shimcha ma'lumotlar (masalan, deeplink uchun packageId, bookingId)
  final DateTime createdAt;
  final bool isRead;

  const AppNotification({
    required this.notificationId,
    required this.recipientUserId,
    required this.type,
    required this.title,
    required this.body,
    this.data,
    required this.createdAt,
    this.isRead = false,
  });

  factory AppNotification.fromJson(Map<String, dynamic> json) {
    return AppNotification(
      notificationId: json['notificationId'] as String,
      recipientUserId: json['recipientUserId'] as String,
      type: AppNotificationType.values.firstWhere((e) => e.toString() == json['type']),
      title: json['title'] as String,
      body: json['body'] as String,
      data: (json['data'] as Map<String, dynamic>?)?.map(
        (k, e) => MapEntry(k, e as String),
      ),
      createdAt: DateTime.parse(json['createdAt'] as String),
      isRead: json['isRead'] as bool? ?? false,
    );
  }

   Map<String, dynamic> toJson() {
    return {
      'notificationId': notificationId,
      'recipientUserId': recipientUserId,
      'type': type.toString(),
      'title': title,
      'body': body,
      'data': data,
      'createdAt': createdAt.toIso8601String(),
      'isRead': isRead,
    };
  }
}
```

**Admin yoki tizim foydalanuvchilarga yoki agentlarga yuborishi mumkin:**
*   **Tizim avtomatik yuboradi:**
    *   Bron tasdiqlanganda/rad etilganda/bekor qilinganda (Foydalanuvchi va Agentga).
    *   Yangi xabar kelganda (Foydalanuvchi va Agentga).
    *   Paket tasdiqlanganda/rad etilganda (Agentga).
*   **Admin yuboradi:**
    *   Platforma yangilanishlari, maxsus takliflar yoki muhim e'lonlar haqida (barcha Foydalanuvchilarga, barcha Agentlarga yoki ma'lum guruhlarga).

### 8. 📊 Boshqaruv Paneli va Statistikalar (Admin va Agent uchun)

**Asosiy metrikalar va ularni hisoblash usullari:**

**Tur Agent uchun Boshqaruv Paneli:**
*   **Jami bronlar soni:** Agentning paketlariga qilingan barcha tasdiqlangan bronlar soni. `Bookings` jadvalidan `agentId` va `status == confirmed` bo'yicha hisoblanadi.
*   **Jami daromad:** Agentning paketlaridan kelgan umumiy daromad (tasdiqlangan va to'langan bronlar). `Bookings` jadvalidan `agentId`, `status == confirmed` (yoki `completed`) va `paymentStatus == paid` bo'yicha `totalPrice` yig'indisi.
*   **O'rtacha baho:** Agentning barcha paketlari uchun o'rtacha baho. `Reviews` jadvalidan `agentId` bo'yicha `rating` yig'indisi / `totalReviews`.
*   **Faol paketlar soni:** Agentning hozirda mavjud va tasdiqlangan paketlari soni. `TourPackages` jadvalidan `agentId` va `isApprovedByAdmin == true` bo'yicha.
*   **Yangi bronlar (oxirgi 7/30 kun):** Ma'lum davr ichidagi yangi bronlar soni.
*   **Eng mashhur paketlar:** Eng ko'p bron qilingan yoki eng yuqori baholangan paketlar ro'yxati.

**Admin uchun Boshqaruv Paneli:**
*   **Jami foydalanuvchilar soni:** Ro'yxatdan o'tgan barcha sayohatchilar soni (`role == traveler`).
*   **Jami agentlar soni:** Ro'yxatdan o'tgan va tasdiqlangan barcha agentlar soni (`role == tourAgent`).
*   **Jami tur paketlar soni:** Platformadagi barcha faol tur paketlar soni.
*   **Jami bronlar soni (platforma bo'yicha):** Platformadagi barcha tasdiqlangan bronlar soni.
*   **Jami daromad (platforma bo'yicha):** Platforma orqali olingan umumiy daromad (komissiya hisobga olinishi mumkin, MVP da shart emas).
*   **O'rtacha paket bahosi (platforma bo'yicha):** Barcha paketlarning o'rtacha bahosi.
*   **Tasdiqlashni kutayotgan agentlar/paketlar soni.**
*   **Eng faol agentlar/foydalanuvchilar.**

Bu metrikalar backendda ma'lumotlar bazasidagi jadvallardan agregat so'rovlar (masalan, `COUNT`, `SUM`, `AVG`) yordamida hisoblanadi.

### 9. 🔐 Autentifikatsiya va Xavfsizlik

*   **JWT yoki Firebase Auth ishlatilishi:**
    *   **Firebase Authentication** MVP uchun tavsiya etiladi, chunki u tez sozlanadi va ko'plab autentifikatsiya usullarini (email/parol, Google, Facebook) qo'llab-quvvatlaydi. U tokenlarni boshqarishni o'z zimmasiga oladi.
    *   Agar maxsus backend (Node.js/NestJS) ishlatilsa, **JWT (JSON Web Tokens)** autentifikatsiya uchun yaxshi tanlov. Foydalanuvchi tizimga kirganda JWT yaratiladi va har bir himoyalangan so'rovda yuboriladi. Backend tokenni tekshiradi.

*   **Parolni xeshlash:**
    *   Agar Firebase Auth ishlatilsa, u parollarni xavfsiz saqlash va xeshlashni avtomatik tarzda amalga oshiradi.
    *   Maxsus backendda parollar bir tomonlama xeshlash algoritmlari (masalan, bcrypt yoki Argon2) yordamida xeshlangan holda ma'lumotlar bazasida saqlanishi kerak. Hech qachon parollarni ochiq matnda saqlamaslik kerak.

*   **Rolga asoslangan kirishni boshqarish (RBAC - Role-Based Access Control):**
    *   Har bir foydalanuvchiga (`UserModel`da `role` maydoni) rol tayinlanadi.
    *   Backend har bir API endpointiga kirishni cheklaydi. Masalan, faqat `TourAgent` rolidagi foydalanuvchi tur paket yarata oladi, faqat `Admin` rolidagi foydalanuvchi foydalanuvchilarni boshqara oladi.
    *   Frontend ham rolga qarab interfeys elementlarini (tugmalar, menyular) ko'rsatishi yoki yashirishi mumkin, ammo asosiy xavfsizlik tekshiruvi har doim backendda amalga oshirilishi shart.

### 10. 🖼️ UX Tavsifi

Bu yerda har bir rol uchun foydalanuvchi sayohatining matnli tavsifi va asosiy ekranlar keltiriladi.

**1. Sayohatchi (Traveler) Foydalanuvchi Sayohati:**

*   **Ro'yxatdan o'tish/Kirish:**
    *   *Ekranlar:* Splash Screen, Kirish ekrani (Login), Ro'yxatdan o'tish ekrani (Sign Up).
    *   *Maqsad:* Foydalanuvchi email/parol bilan yoki ijtimoiy tarmoqlar orqali (MVPda email/parol yetarli) hisob yaratadi yoki mavjud hisobiga kiradi.
*   **Asosiy Ekran (Home):**
    *   *Ekranlar:* Asosiy ekran.
    *   *Maqsad:* Mashhur tur paketlarni, maxsus takliflarni, qidiruv panelini va kategoriyalarni ko'rsatadi.
    *   *Elementlar:* Qidiruv satri, kategoriya filtrlari (tog', dengiz, shahar), tavsiya etilgan paketlar kartalari, maxsus takliflar banneri.
*   **Qidiruv va Natijalar:**
    *   *Ekranlar:* Qidiruv natijalari ekrani.
    *   *Maqsad:* Foydalanuvchi kalit so'z, manzil, sana yoki kategoriya bo'yicha paketlarni qidiradi. Natijalar ro'yxat yoki kartochkalar ko'rinishida chiqadi. Filtrlash (narx, reyting, davomiylik) va saralash imkoniyati.
*   **Tur Paket Tafsilotlari:**
    *   *Ekranlar:* Paket tafsilotlari ekrani.
    *   *Maqsad:* Tanlangan paket haqida to'liq ma'lumot: rasmlar galereyasi, tavsif, narx, kiritilgan xizmatlar, marshrut, agent ma'lumotlari, sharhlar va baholar.
    *   *Elementlar:* "Bron qilish" tugmasi, "Agent bilan bog'lanish" tugmasi.
*   **Bron Qilish Jarayoni:**
    *   *Ekranlar:* Bron qilish formasi, To'lov ekrani, Tasdiqlash ekrani.
    *   *Maqsad:* Sayohatchilar sonini kiritish, maxsus talablarni yozish, umumiy narxni ko'rish. To'lov ma'lumotlarini kiritish (MVPda oddiy "to'lov amalga oshirildi" deb belgilash mumkin, haqiqiy to'lov integratsiyasi keyinroq). Bron tasdiqlanganligi haqida xabar.
*   **Mening Profilim:**
    *   *Ekranlar:* Profil ekrani, Mening bronlarim, Mening sharhlarim, Sozlamalar.
    *   *Maqsad:* Shaxsiy ma'lumotlarni tahrirlash, bronlar tarixini ko'rish, qoldirilgan sharhlarni ko'rish, ilova sozlamalarini o'zgartirish (masalan, bildirishnomalar).
*   **Xabarlar:**
    *   *Ekranlar:* Suhbatlar ro'yxati ekrani, Chat ekrani.
    *   *Maqsad:* Agentlar bilan yozishmalarni ko'rish va yangi xabar yuborish.

**2. Tur Agent Foydalanuvchi Sayohati:**

*   **Ro'yxatdan o'tish/Kirish:**
    *   *Ekranlar:* Kirish ekrani, Ro'yxatdan o'tish ekrani (agentlik ma'lumotlari bilan). Admin tasdig'ini kutish xabari.
    *   *Maqsad:* Agent sifatida ro'yxatdan o'tish yoki tizimga kirish.
*   **Agent Boshqaruv Paneli (Dashboard):**
    *   *Ekranlar:* Agent Dashboard ekrani.
    *   *Maqsad:* Asosiy statistik ma'lumotlarni (bronlar soni, daromad, reyting), paketlar ro'yxatini va yangi bronlar haqida bildirishnomalarni ko'rsatadi.
    *   *Elementlar:* "Yangi paket qo'shish" tugmasi, statistika vidjetlari.
*   **Paketlarni Boshqarish (CRUD):**
    *   *Ekranlar:* Paketlar ro'yxati ekrani, Paket yaratish/tahrirlash formasi.
    *   *Maqsad:* Yangi tur paketlarni qo'shish, mavjudlarini tahrirlash yoki o'chirish. Paket ma'lumotlarini (nomi, tavsifi, narxi, rasmlari, sanalari) kiritish.
*   **Bronlarni Boshqarish:**
    *   *Ekranlar:* Bronlar ro'yxati ekrani, Bron tafsilotlari ekrani.
    *   *Maqsad:* O'z paketlariga kelib tushgan bronlarni ko'rish, ularni tasdiqlash yoki rad etish. Sayohatchi ma'lumotlarini ko'rish.
*   **Xabarlar:**
    *   *Ekranlar:* Suhbatlar ro'yxati ekrani, Chat ekrani.
    *   *Maqsad:* Sayohatchilar bilan yozishmalarni ko'rish va javob berish.
*   **Agent Profili:**
    *   *Ekranlar:* Agent profilini tahrirlash ekrani.
    *   *Maqsad:* Agentlik ma'lumotlari, aloqa vositalari, litsenziya kabi ma'lumotlarni yangilash.

**3. Admin Foydalanuvchi Sayohati:**

*   **Kirish:**
    *   *Ekranlar:* Admin Kirish ekrani (alohida yoki umumiy kirish oynasida rol tanlash).
    *   *Maqsad:* Maxsus admin hisobi bilan tizimga kirish.
*   **Admin Boshqaruv Paneli (Dashboard):**
    *   *Ekranlar:* Admin Dashboard ekrani.
    *   *Maqsad:* Umumiy platforma statistikasini (jami foydalanuvchilar, agentlar, paketlar, bronlar), tasdiqlashni kutayotgan so'rovlarni ko'rsatadi.
*   **Foydalanuvchilarni Boshqarish:**
    *   *Ekranlar:* Foydalanuvchilar ro'yxati ekrani (Sayohatchilar va Agentlar alohida tablarda bo'lishi mumkin).
    *   *Maqsad:* Barcha foydalanuvchilarni ko'rish, qidirish, ularning ma'lumotlarini ko'rish, bloklash/faollashtirish, rollarini o'zgartirish.
*   **Agentlarni Tasdiqlash:**
    *   *Ekranlar:* Agent so'rovlari ro'yxati ekrani.
    *   *Maqsad:* Yangi ro'yxatdan o'tgan agentlarning so'rovlarini ko'rib chiqish va tasdiqlash yoki rad etish.
*   **Tur Paketlarni Boshqarish/Moderatsiya:**
    *   *Ekranlar:* Barcha paketlar ro'yxati ekrani.
    *   *Maqsad:* Barcha tur paketlarni ko'rish, ularni tahrirlash, o'chirish. Agentlar tomonidan qo'shilgan yangi paketlarni tasdiqlash yoki rad etish.
*   **Sharhlarni Moderatsiya qilish:**
    *   *Ekranlar:* Sharhlar ro'yxati ekrani.
    *   *Maqsad:* Qoidalarga zid bo'lgan yoki spam sharhlarni ko'rib chiqish va o'chirish.
*   **Bildirishnomalar Yuborish:**
    *   *Ekranlar:* Bildirishnoma yaratish formasi.
    *   *Maqsad:* Ma'lum guruh foydalanuvchilariga (barcha sayohatchilar, barcha agentlar) umumiy xabarlar yoki e'lonlar yuborish.

### 11. 🧱 Texnologik Stek Tavsiyasi

*   **Frontend (Mobil Ilova):**
    *   **Flutter:** Kross-platformalilik (iOS va Android uchun bitta kod bazasi), tezkor rivojlantirish, boy vidjetlar to'plami va yaxshi performans tufayli tavsiya etiladi. Dart dasturlash tilidan foydalaniladi.

*   **Backend:**
    *   **Firebase (BaaS - Backend as a Service):** MVP uchun eng yaxshi tanlov.
        *   **Firestore:** NoSQL hujjatlar bazasi, real vaqtda sinxronizatsiya, masshtablanuvchanlik.
        *   **Firebase Functions:** Server tomonidagi logikani (masalan, bildirishnomalar yuborish, murakkab hisob-kitoblar) amalga oshirish uchun serverless funksiyalar.
        *   **Firebase Authentication:** Tayyor autentifikatsiya yechimi.
        *   **Firebase Storage:** Rasmlar va boshqa fayllarni saqlash uchun.
        *   **Firebase Cloud Messaging (FCM):** Push bildirishnomalar uchun.
    *   *Alternativ (Agar ko'proq nazorat va murakkablik kerak bo'lsa):*
        *   **Node.js bilan NestJS frameworki:** TypeScript asosidagi, modulli va masshtablanuvchan backend yaratish uchun kuchli vosita.
        *   **Python bilan Django/Flask:** Yana bir mashhur backend tanlovi.

*   **Ma'lumotlar Bazasi:**
    *   **Firestore (Firebase bilan birga):** Yuqorida aytilganidek, NoSQL, hujjatga yo'naltirilgan baza.
    *   *Alternativ (Agar NestJS/Django kabi an'anaviy backend tanlansa):*
        *   **PostgreSQL:** Kuchli, ochiq manbali relyatsion ma'lumotlar bazasi. Murakkab so'rovlar va ma'lumotlar yaxlitligi uchun yaxshi.
        *   **MongoDB:** Hujjatga yo'naltirilgan NoSQL baza, agar Firestorega o'xshash moslashuvchanlik kerak bo'lsa.

*   **Autentifikatsiya:**
    *   **Firebase Authentication:** Tavsiya etiladi.

*   **Push Bildirishnomalar:**
    *   **Firebase Cloud Messaging (FCM):** Bepul va Flutter bilan yaxshi integratsiyalashgan.

*   **Bulutli Rasm Saqlash:**
    *   **Firebase Storage:** Tavsiya etiladi.
    *   *Alternativ:* AWS S3, Cloudinary.

**MVP uchun Firebase to'plami (Flutter + Firebase) eng tezkor va kam xarajatli yechim bo'ladi.** Bu startapga tezda bozorga chiqish va foydalanuvchi fikr-mulohazalariga asoslanib mahsulotni takomillashtirish imkonini beradi.

---

Ushbu SRS hujjati MVP bosqichi uchun asosiy talablarni qamrab oladi. Loyiha rivojlanishi bilan qo'shimcha funksiyalar va talablar kiritilishi mumkin.
