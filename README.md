# Flutter 101: สร้าง **Category App** ธีมหรูหรา Navy & Cream (Modern + API JSON)

> เหมาะสำหรับผู้เริ่มเขียน Flutter ครั้งแรก เป้าหมายคือทำแอพระบุ “หมวดหมู่ (Category)” เพื่อฝึกสร้าง widget, วาง layout, และเชื่อม API (เริ่มจาก Mock JSON ก่อน)

---

## ภาพรวมและแนวทาง
- เทคโนโลยี: **Flutter (Dart)**, **Material 3**
- ธีมสีหลัก: Navy `#002147`, Cream `#d2b48c`
- โครงสร้างหน้าจอแรก: หน้า Home แสดงการ์ดหมวดหมู่แบบ Grid (2 คอลัมน์) → เน้นความ “Luxury + Modern”
- ขั้นตอน: ตั้งค่าโปรเจกต์ → ออกแบบ UX/Wireframe → วาง Theme → สร้าง UI ด้วย Mock JSON → เปลี่ยนไปใช้ API จริงภายหลัง

---

## Step 0 — ติดตั้งและเตรียมเครื่องมือ (ครั้งเดียว)
1) ติดตั้ง Flutter SDK (ตามระบบปฏิบัติการของคุณ) และ Android Studio **หรือ** VS Code (พร้อม Flutter/Dart extensions)
2) เปิด Terminal แล้วตรวจสอบว่าใช้ได้:
   ```bash
   flutter --version
   flutter doctor
   ```
3) สร้างโปรเจกต์ใหม่ (ชื่ออะไรก็ได้ เช่น `luxury_category_app`):
   ```bash
   flutter create luxury_category_app
   cd luxury_category_app
   ```
4) รันบนอีมูเลเตอร์หรือโทรศัพท์จริง:
   ```bash
   flutter run
   ```

> ถ้ารันได้แปลว่าพร้อมลุยขั้นต่อไปแล้ว ✨

---

## Step 1 — โครง UX/UI (Low‑fi Wireframe)

ก่อนเขียนโค้ด ลองวางภาพรวมหน้าจอแบบง่าย ๆ:

```
┌──────────────────────────────┐
│      AppBar: "หมวดหมู่สุดหรู" │  ← พื้นหลัง Navy ตัวหนังสือสีขาว
├──────────────────────────────┤
│  GridView (2 คอลัมน์)         │  ← การ์ดโทนขาว + แซม Cream / Navy
│  ┌──────────┐  ┌──────────┐    │
│  │  Image   │  │  Image   │    │ ← ส่วนหัวการ์ด: Gradient Navy→Cream
│  │          │  │          │    │    + ไอคอน category กลาง ๆ
│  └──────────┘  └──────────┘    │
│  Title (สี Navy) + CTA        │ ← ปุ่ม/ลิงก์ “ดูรายละเอียด” โทน Cream
│  …                             │
└──────────────────────────────┘
```

---

## Step 2 — โค้ดเริ่มต้น (Theme + หน้าหลัก + Mock JSON)

> ไปที่ไฟล์ `lib/main.dart` แล้ว **แทนที่ทั้งหมด** ด้วยโค้ดด้านล่าง (มีคอมเมนต์กำกับทุกบรรทัด)**

```dart
// main.dart — เวอร์ชันสอนทีละบรรทัด พร้อม Mock JSON

import 'dart:convert'; // ใช้ถอดรหัสข้อความ JSON → Object
import 'package:flutter/material.dart'; // นำเข้า Flutter UI toolkit

void main() { // จุดเริ่มโปรแกรม
  runApp(const MyApp()); // สั่งรัน widget หลักของแอพ
}

class MyApp extends StatelessWidget { // ตัวแอพระดับบนสุด (Stateless)
  const MyApp({super.key}); // คอนสตรัคเตอร์พร้อม key มาตรฐาน

  // กำหนดค่าสีประจำแบรนด์ของเรา
  static const Color kNavy = Color(0xFF002147); // Navy (Primary)
  static const Color kCream = Color(0xFFD2B48C); // Cream (Secondary/Accent)

  @override
  Widget build(BuildContext context) { // ฟังก์ชันสร้าง UI
    // นิยาม ColorScheme แบบกำหนดเอง เพื่อคุมคอนทราสต์/การมองเห็น
    final colorScheme = ColorScheme(
      brightness: Brightness.light, // โหมดพื้นฐานสว่าง
      primary: kNavy, // สีหลัก: Navy
      onPrimary: Colors.white, // สีตัวอักษรบนพื้น primary
      secondary: kCream, // สีรอง: Cream
      onSecondary: Colors.black87, // สีตัวอักษรบนพื้น secondary
      error: Colors.red.shade700, // สีแจ้ง Error
      onError: Colors.white, // สีตัวอักษรบนพื้น error
      background: const Color(0xFFF7F3EC), // สีพื้นหลังนุ่ม ๆ โทนอุ่น
      onBackground: Colors.black87, // สีตัวอักษรบนพื้นหลัง
      surface: Colors.white, // สีพื้นผิว card/แผงต่าง ๆ
      onSurface: Colors.black87, // สีตัวอักษรบนพื้นผิว
    );

    return MaterialApp( // รากของ MaterialApp
      title: 'Luxury Categories', // ชื่อแอพ (บางที่ใช้ใน task switcher)
      debugShowCheckedModeBanner: false, // ปิดป้าย DEBUG มุมขวาบน
      theme: ThemeData( // ธีมหลักของแอพ
        useMaterial3: true, // ใช้ Material 3 (ดีไซน์ล่าสุด)
        colorScheme: colorScheme, // ใช้สีกลางที่กำหนดไว้ด้านบน
        scaffoldBackgroundColor: colorScheme.background, // สีพื้นหลังหน้าจอ
        appBarTheme: AppBarTheme( // สไตล์ AppBar ส่วนหัว
          backgroundColor: colorScheme.primary, // พื้นหัว Navy
          foregroundColor: colorScheme.onPrimary, // ตัวหนังสือ/ไอคอนสีขาว
          elevation: 0, // เรียบหรูไม่ยกนูน
          centerTitle: true, // จัดกึ่งกลางชื่อเรื่อง
          titleTextStyle: const TextStyle( // ฟอนต์ของชื่อเรื่อง
            fontSize: 20,
            fontWeight: FontWeight.w600,
            color: Colors.white,
          ),
        ),
        cardTheme: CardTheme( // ธีมของการ์ดหมวดหมู่
          color: Colors.white, // พื้นการ์ดสีขาว
          elevation: 3, // เงาเบา ๆ ดูพรีเมียม
          margin: const EdgeInsets.all(8), // เว้นขอบรอบการ์ด
          shape: RoundedRectangleBorder( // มุมโค้งสวย
            borderRadius: BorderRadius.circular(16),
          ),
        ),
        textTheme: const TextTheme( // กำหนดน้ำหนักตัวหนังสือพื้นฐาน
          headlineSmall: TextStyle(fontWeight: FontWeight.w700),
          titleMedium: TextStyle(fontWeight: FontWeight.w600),
        ),
        elevatedButtonTheme: ElevatedButtonThemeData( // ธีมปุ่มยก
          style: ElevatedButton.styleFrom(
            backgroundColor: kNavy, // ปุ่มสี Navy
            foregroundColor: Colors.white, // ตัวหนังสือปุ่มสีขาว
            shape: RoundedRectangleBorder(
              borderRadius: BorderRadius.circular(14), // มุมโค้งสไตล์โมเดิร์น
            ),
            padding: const EdgeInsets.symmetric(
              horizontal: 16,
              vertical: 12,
            ), // ระยะห่างภายในปุ่ม
          ),
        ),
      ),
      home: const HomeScreen(), // หน้าหลักของเรา
    );
  }
}

class HomeScreen extends StatefulWidget { // หน้าแรกแบบ Stateful (เพราะรอโหลดข้อมูล)
  const HomeScreen({super.key});
  @override
  State<HomeScreen> createState() => _HomeScreenState(); // ผูก State
}

class _HomeScreenState extends State<HomeScreen> {
  late Future<List<Category>> _future; // ตัวแปรเก็บ Future ของรายการหมวดหมู่

  @override
  void initState() { // เริ่มต้นเมื่อ widget ถูกสร้าง
    super.initState();
    _future = CategoryService().fetchCategories(); // โหลดข้อมูล (Mock JSON)
  }

  @override
  Widget build(BuildContext context) { // UI ของหน้า Home
    final cs = Theme.of(context).colorScheme; // ดึงชุดสีมาใช้สะดวก
    return Scaffold( // โครงหน้าจอหลัก
      appBar: AppBar(
        title: const Text('หมวดหมู่สุดหรู'), // ข้อความหัวหน้าจอ
      ),
      body: Padding( // เว้นขอบรอบเนื้อหา
        padding: const EdgeInsets.all(16),
        child: FutureBuilder<List<Category>>( // รอผลโหลดข้อมูลแบบ Async
          future: _future, // ชี้ไปยัง Future ที่ประกาศไว้
          builder: (context, snapshot) { // สร้าง UI ตามสถานะของ Future
            if (snapshot.connectionState == ConnectionState.waiting) {
              return const Center(child: CircularProgressIndicator()); // กำลังโหลด
            }
            if (snapshot.hasError) { // ถ้าเกิดข้อผิดพลาด
              return Center(child: Text('เกิดข้อผิดพลาด: ${snapshot.error}'));
            }
            final items = snapshot.data ?? []; // ได้ข้อมูลหรือยัง? ถ้าไม่ ให้ลิสต์ว่าง
            if (items.isEmpty) { // ถ้าไม่มีข้อมูล
              return const Center(child: Text('ไม่มีข้อมูลหมวดหมู่'));
            }
            return GridView.builder( // แสดงผลเป็นตาราง 2 คอลัมน์
              gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
                crossAxisCount: 2, // 2 คอลัมน์
                mainAxisSpacing: 16, // ระยะห่างแนวตั้ง
                crossAxisSpacing: 16, // ระยะห่างแนวนอน
                childAspectRatio: 4 / 5, // อัตราส่วนกว้าง:สูงของการ์ด
              ),
              itemCount: items.length, // จำนวนการ์ดตามข้อมูล
              itemBuilder: (context, i) {
                return CategoryCard(category: items[i]); // วาดการ์ดแต่ละใบ
              },
            );
          },
        ),
      ),
    );
  }
}

class CategoryCard extends StatelessWidget { // การ์ดหมวดหมู่แบบแยกเป็น Widget
  final Category category; // ข้อมูลของการ์ดใบนี้
  const CategoryCard({super.key, required this.category}); // รับพารามิเตอร์

  @override
  Widget build(BuildContext context) {
    final cs = Theme.of(context).colorScheme; // ดึงสีใช้งาน
    return Card( // การ์ดสวย ๆ ตาม Theme
      clipBehavior: Clip.antiAlias, // ตัดมุมภาพตามขอบการ์ด
      child: InkWell( // ทำให้กดแล้วมีริปเปิล
        onTap: () { /* TODO: นำทางไปหน้ารายละเอียด */ }, // จุดสำหรับต่อยอด
        child: Column( // วางองค์ประกอบแนวตั้ง
          crossAxisAlignment: CrossAxisAlignment.start, // ชิดซ้าย
          children: [
            Container( // ส่วนหัวการ์ด (ภาพ/แบ็กกราวด์)
              height: 120, // ความสูงหัวการ์ด
              width: double.infinity, // กว้างเต็มการ์ด
              decoration: BoxDecoration(
                gradient: LinearGradient( // ไล่เฉด Navy→Cream ดูหรู
                  colors: [cs.primary, cs.secondary],
                  begin: Alignment.topLeft,
                  end: Alignment.bottomRight,
                ),
              ),
              child: Center(
                child: Icon(
                  Icons.category, // ไอคอนหมวดหมู่กลางภาพ
                  size: 48, // ขนาดไอคอน
                  color: cs.onPrimary.withOpacity(0.9), // สีไอคอนขาวโปร่งนิด ๆ
                ),
              ),
            ),
            Padding( // ช่องว่างรอบชื่อหมวดหมู่
              padding: const EdgeInsets.all(12),
              child: Text(
                category.title, // แสดงชื่อหมวดหมู่จากข้อมูล
                style: Theme.of(context).textTheme.titleMedium?.copyWith(
                  color: cs.primary, // ใช้สี Navy ให้ความรู้สึกแบรนด์
                  letterSpacing: 0.2, // ระยะห่างตัวอักษรเล็กน้อย
                ),
              ),
            ),
            const Spacer(), // ดันส่วนล่างชิดขอบท้ายการ์ด
            Padding( // แถวล่าง: CTA + ลูกศร
              padding: const EdgeInsets.symmetric(horizontal: 12, vertical: 12),
              child: Row(
                children: [
                  Text(
                    'ดูรายละเอียด', // ข้อความ CTA
                    style: TextStyle(
                      color: cs.secondary, // โทน Cream ให้โดดเด่นพอประมาณ
                      fontWeight: FontWeight.w600,
                    ),
                  ),
                  const Spacer(), // เว้นที่ดันไอคอนไปด้านขวาสุด
                  Container(
                    decoration: BoxDecoration(
                      color: cs.primary.withOpacity(0.08), // พื้น Navy โปร่งใส
                      borderRadius: BorderRadius.circular(12), // มุมโค้งเล็กน้อย
                    ),
                    padding: const EdgeInsets.all(8), // ขนาดปุ่มไอคอน
                    child: Icon(
                      Icons.chevron_right, // ลูกศรไปขวา
                      color: cs.primary, // สี Navy ให้สัมพันธ์กับแบรนด์
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class Category { // โมเดลข้อมูล Category
  final String id; // ไอดีหมวดหมู่
  final String title; // ชื่อหมวดหมู่
  final String imageUrl; // URL รูปภาพ (ตอนนี้ยังไม่ได้ใช้)

  Category({ // คอนสตรัคเตอร์ต้องระบุทุกฟิลด์
    required this.id,
    required this.title,
    required this.imageUrl,
  });

  factory Category.fromJson(Map<String, dynamic> json) { // แปลง Map → Category
    return Category(
      id: json['id']?.toString() ?? '', // ป้องกัน null → string
      title: json['title'] ?? '', // ถ้าไม่มี title ให้ค่าว่าง
      imageUrl: json['imageUrl'] ?? '', // ถ้าไม่มีรูปก็ให้ค่าว่าง
    );
  }
}

class CategoryService { // ชั้นบริการ (service) สำหรับดึงข้อมูล
  Future<List<Category>> fetchCategories() async { // ดึงลิสต์ Category แบบ async
    await Future.delayed(const Duration(milliseconds: 600)); // จำลองดีเลย์เน็ต
    final list = jsonDecode(_mockJson) as List<dynamic>; // แปลง JSON → List<dynamic>
    // map แต่ละ item (Map<String, dynamic>) → Category แล้วรวมเป็น List
    return list
        .map((e) => Category.fromJson(e as Map<String, dynamic>))
        .toList();
  }
}

// Mock JSON: จำลองข้อมูลจาก API จริง (รูปแบบ JSON ที่คุณมีอยู่)
const String _mockJson = '''
[
  {"id": 1, "title": "Luxury Travel", "imageUrl": ""},
  {"id": 2, "title": "Fine Dining", "imageUrl": ""},
  {"id": 3, "title": "Exclusive Events", "imageUrl": ""},
  {"id": 4, "title": "Designer Fashion", "imageUrl": ""}
]
''';
```

> บันทึกไฟล์ แล้วรัน `flutter run` → ควรเห็น Grid การ์ด 4 ใบสวย ๆ ในธีม Navy & Cream

---

## Step 3 — ปรับโครงโค้ดให้เป็นระเบียบ (แนะนำเมื่อเริ่มคุ้นมือ)

เมื่อเริ่มใหญ่ขึ้น แยกไฟล์ตามหน้าที่จะช่วยดูแลง่ายขึ้น:

```
lib/
├─ main.dart            ← จุดเริ่มต้น + Theme + route แรก
├─ models/
│  └─ category.dart     ← คลาสโมเดล Category
├─ services/
│  └─ category_service.dart  ← เรียก API/Mock JSON
└─ screens/
   └─ home_screen.dart  ← หน้าหลัก GridView
```

> ณ ตอนนี้เราเขียนรวมในไฟล์เดียวเพื่อความเข้าใจง่ายก่อน จากนั้นค่อยย้ายตามโครงนี้

---

## Step 4 — สลับจาก Mock JSON → API จริง (เมื่อพร้อม)

> ถ้าคุณมี API จริง (คืนค่าเป็น JSON) ให้ใช้แพ็กเกจ `http` (นิยม) แล้วแก้ `CategoryService` ดังนี้

### 4.1 เพิ่ม dependency ใน `pubspec.yaml`
```yaml
# ส่วน dependencies:
dependencies:
  flutter:
    sdk: flutter
  http: ^1.0.0  # หรือเวอร์ชันล่าสุดที่รองรับ
```

จากนั้นรัน:
```bash
flutter pub get
```

### 4.2 ตัวอย่าง `CategoryService` แบบเรียก API จริง
```dart
import 'dart:convert'; // สำหรับ jsonDecode
import 'package:http/http.dart' as http; // แพ็กเกจ http

class CategoryService {
  final String baseUrl;
  CategoryService({this.baseUrl = 'https://example.com/api'}); // แก้ให้ตรงระบบของคุณ

  Future<List<Category>> fetchCategories() async {
    final url = Uri.parse('$baseUrl/categories'); // endpoint จริง
    final res = await http.get(url); // ยิง GET
    if (res.statusCode != 200) { // เช็คสถานะ HTTP
      throw Exception('โหลดข้อมูลไม่สำเร็จ: ${res.statusCode}');
    }
    final list = jsonDecode(res.body) as List<dynamic>; // Body → List
    return list
        .map((e) => Category.fromJson(e as Map<String, dynamic>))
        .toList();
  }
}
```

> ถ้า API ของคุณมีโครงสร้างต่างไป เช่น `{ data: [...] }` ก็ปรับจุด `jsonDecode` ให้เข้าถึง `['data']`

---

## Step 5 — เคล็ดลับให้ดู “Luxury + Modern” มากขึ้น
- ใช้ **ระยะห่าง** (padding/margin) ที่พอดี — เราใช้ 12–16 px เป็นหลัก ดูสะอาด
- **Shadow เบา ๆ** และ **โค้งมนกำลังดี** (รัศมี ~12–16) ช่วยให้พรีเมียม
- **ตัวอักษรหัวเรื่อง** หนัก (w600–w700) สี Navy, เนื้อหาสี onSurface
- ใช้ **Gradient Navy→Cream** เฉพาะพื้นที่นำสายตา (header การ์ด) เพื่อไม่ให้เยอะเกิน
- ถ้าต้องการยกระดับฟอนต์: ใช้ `google_fonts` (เช่น Playfair Display + Inter) ได้ แต่เริ่มต้นให้รันง่าย ๆ ก่อน

### ตัวอย่างเพิ่มฟอนต์ (ไม่จำเป็นต้องทำตอนนี้)
```yaml
# pubspec.yaml
dependencies:
  google_fonts: ^6.0.0
```
```dart
// ที่ theme:
textTheme: GoogleFonts.interTextTheme().copyWith(
  titleMedium: GoogleFonts.playfairDisplay(
    fontWeight: FontWeight.w600,
    fontSize: 16,
  ),
),
```

---

## Step 6 — สิ่งที่ควรลองต่อยอด
1) **หน้ารายละเอียด Category** เมื่อกดการ์ด → ไปหน้าใหม่ แสดงรายการย่อย
2) **Pull‑to‑Refresh**: หุ้ม Grid ด้วย `RefreshIndicator` → ดึงลงเพื่อโหลดใหม่
3) **State Management** เบื้องต้น: เริ่มจาก `setState` ก่อน แล้วค่อยศึกษา `Provider`, `Riverpod` ภายหลัง
4) **แยกไฟล์ + Unit Test**: เมื่อเข้าใจแล้วให้แยกไฟล์ตามโครงที่แนะนำ และลองเขียนเทสต์เล็ก ๆ สำหรับ `Category.fromJson`

---

## สรุปสิ่งที่ได้ฝึก
- การตั้งค่าธีมสี (Navy/Cream) ให้ mood & tone หรูหรา
- สร้าง UI สไตล์โมเดิร์นด้วย `GridView` + `Card`
- ออกแบบโครง UX ง่าย ๆ แล้วแปลงเป็น Widget
- อ่านข้อมูลจาก Mock JSON ด้วย `jsonDecode`
- เตรียมพร้อมเปลี่ยนเป็น API จริงด้วยแพ็กเกจ `http`

> หากรันแล้วติดที่ขั้นไหน ให้บอกอาการ/ภาพหน้าจอ/ข้อความ error มาได้เลย เดี๋ยวช่วยไล่ให้ทีละจุดครับ

