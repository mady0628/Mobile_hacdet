# I. Components

**Android Components** là những thành phần cơ bản được sử dụng để xây dựng một ứng dụng Android. Mỗi thành phần đảm nhiệm một nhiệm vụ riêng, chẳng hạn như hiển thị giao diện, xử lý dữ liệu, chạy tác vụ nền hoặc nhận thông báo từ hệ thống.

Trong Android, có 4 thành phần chính:
- Activity
- Service
- Broadcast Receiver
- Content Provider

## 1. Activity

**Activity** đại diện cho một màn hình duy nhất với giao diện người dùng (UI). Ví dụ: một ứng dụng email có thể có một Activity hiển thị danh sách email, một Activity khác để soạn email.

**Tác dụng:** Xử lý tương tác của người dùng, hiển thị giao diện, điều hướng giữa các màn hình.

```java
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val btnClick = findViewById<Button>(R.id.btnClick)
        btnClick.setOnClickListener {
            Toast.makeText(this, "Đã bấm nút trên Activity!", Toast.LENGTH_SHORT).show()
        }
    }
}
```

## 2. Service

**Service** là thành phần chạy nền (background) để thực hiện các tác vụ tốn thời gian hoặc công việc cho các tiến trình từ xa mà không cần cung cấp giao diện người dùng.

**Tác dụng:** Phát nhạc dưới nền, tải tệp tin từ mạng, đồng bộ dữ liệu định kỳ.

## 3. Broadcast Receiver

**Broadcast Receiver** cho phép ứng dụng phản hồi lại các thông báo (broadcast) từ hệ thống hoặc từ các ứng dụng khác, ngay cả khi ứng dụng của bạn không chạy.

**Tác dụng:** Lắng nghe sự kiện pin yếu, mất kết nối mạng, khởi động xong thiết bị (boot completed), hoặc nhận tin nhắn SMS.

```java
class PowerConnectionReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action == Intent.ACTION_POWER_CONNECTED) {
            Toast.makeText(context, "Thiết bị đã được cắm sạc!", Toast.LENGTH_SHORT).show()
        } else if (intent.action == Intent.ACTION_POWER_DISCONNECTED) {
            Toast.makeText(context, "Thiết bị đã rút sạc!", Toast.LENGTH_SHORT).show()
        }
    }
}
```

## 4. Content Provider

**Content Provider** quản lý một tập hợp dữ liệu được chia sẻ của ứng dụng. Có thể lưu dữ liệu trong hệ thống tệp, cơ sở dữ liệu SQLite, hoặc trên web.

**Tác dụng:** Cho phép các ứng dụng khác truy vấn hoặc sửa đổi dữ liệu (nếu được cấp quyền). Ví dụ như Content Provider quản lý danh bạ của thiết bị.

```java
fun getDeviceContacts(context: Context) {
    val cursor = context.contentResolver.query(
        ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
        null, null, null, null
    )
    cursor?.use {
        val nameIndex = it.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME)
        val numberIndex = it.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER)
        while (it.moveToNext()) {
            val name = it.getString(nameIndex)
            val number = it.getString(numberIndex)
        }
    }
}
```

# II. Khung xương của một project Android

## 1. Manifest

Đường dẫn: app/src/main/AndroidManifest.xml

Tác dụng: Khai báo các thành phần cốt lõi (Activity, Service, Receiver), các quyền (Permissions) cần thiết (như Internet, Camera), cấu hình phần cứng yêu cầu, và định nghĩa Activity nào sẽ chạy đầu tiên khi mở app

## 2. Kotlin Source

Đường dẫn: app/src/main/kotlin/

Tác dụng: Nơi chứa toàn bộ mã nguồn logic của ứng dụng. Thường được phân chia thành các package theo tính năng hoặc kiến trúc

## 3. Drawable 

Đường dẫn: app/src/main/res/drawable/

Tác dụng: Chứa các tài nguyên đồ họa: hình ảnh (PNG, JPEG), tệp vector (XML), hoặc các file XML định nghĩa hình dạng (shape), trạng thái nút bấm (selector).

## 4. Layout

Đường dẫn: app/src/main/res/layout/

Tác dụng: Chứa các tệp XML định nghĩa giao diện (UI) cho Activity hoặc Fragment.

## 5. Mipmap

Đường dẫn: app/src/main/res/mipmap/

Tác dụng: Chuyên dùng để chứa icon của ứng dụng (App Icon) ở nhiều độ phân giải khác nhau (hdpi, xhdpi, xxhdpi) để hiển thị sắc nét trên màn hình chính của launcher.

## 6. Values 

Đường dẫn: app/src/main/res/values/

Tác dụng: Chứa các file XML định nghĩa các giá trị tĩnh: strings.xml (chuỗi văn bản để đa ngôn ngữ), colors.xml (mã màu), themes.xml hoặc styles.xml (giao diện, theme của app).

## 7. Build.gradle (Project)

Cấu hình cho toàn bộ dự án: khai báo các kho lưu trữ (repositories như Maven, Google) và các dependency áp dụng cho mọi module.

## 8. Guild.gradle (Module)

Cấu hình build cụ thể cho ứng dụng: Application ID (tên gói), minSDK (phiên bản Android hỗ trợ thấp nhất), targetSDK, phiên bản ứng dụng (versionCode, versionName) và danh sách các thư viện (dependencies) được sử dụng.

# III. Vòng đời của một ứng dụng

Trong Android, thành phần phản ánh rõ nhất "vòng đời" chính là Activity. Hệ điều hành quản lý Activity qua một cỗ máy trạng thái (state machine) được kích hoạt bởi các hàm callback (gọi lại).

Các trạng thái (States)
1. Initialized: Activity được khởi tạo nhưng chưa thực sự được tạo ra trên hệ thống.
2. Created: Activity đã được tạo, giao diện đã được nạp, nhưng chưa hiển thị cho người dùng.
3. Started: Activity bắt đầu hiển thị trên màn hình nhưng chưa có tiêu điểm (focus) để tương tác.
4. Resumed: Activity hiển thị hoàn toàn trên cùng và người dùng có thể tương tác trực tiếp.
5. Destroyed: Activity bị hủy bỏ hoàn toàn khỏi bộ nhớ.

Các hàm Callback vòng đời tương ứng
- onCreate(): Được gọi duy nhất một lần khi Activity được tạo. Tác dụng: Khởi tạo UI (`setContentView`), liên kết ViewModel, lấy dữ liệu intent truyền sang.
- onStart(): Gọi khi Activity bắt đầu hiển thị cho người dùng. Tác dụng: Chuẩn bị các thành phần hiển thị, nhưng người dùng chưa chạm vào được.
- onResume(): Gọi khi Activity bắt đầu tương tác với người dùng. Tác dụng: Bắt đầu các animation, mở camera, bắt đầu lắng nghe cảm biến.
- onPause(): Gọi khi Activity mất focus nhưng vẫn hiển thị một phần (ví dụ có một dialog trong suốt hiện lên, hoặc chia đôi màn hình). Tác dụng: Tạm dừng video, dừng animation, lưu các trạng thái UI nhỏ giọt. Quá trình này phải thực thi cực nhanh.
- onStop(): Gọi khi Activity không còn hiển thị với người dùng nữa (bị Activity khác đè lên hoặc người dùng bấm nút Home). Tác dụng: Giải phóng các tài nguyên nặng (như ngắt kết nối database, dừng vị trí GPS) để tiết kiệm pin.
- onDestroy(): Gọi trước khi Activity bị hủy hoàn toàn (do gọi hàm finish() hoặc do hệ thống cần thu hồi bộ nhớ). Tác dụng: Xóa bỏ hoàn toàn các luồng (thread) chạy ngầm, hủy đăng ký các listener để tránh rò rỉ bộ nhớ (Memory Leak).
- onRestart(): Gọi khi Activity đã bị onStop() trước đó nhưng giờ được gọi hiển thị lại (người dùng mở lại app từ Recent Apps). Sau hàm này, hệ thống sẽ gọi onStart().

# IV. Kiến trúc Android

## 1. Kiến trúc tổng quan của Android (Android Architecture)
Hệ điều hành Android được xây dựng theo mô hình phân lớp (Layered Architecture), gồm 5 tầng chính từ thấp lên cao:

**Linux Kernel (Tầng nhân Linux):**

- Nằm ở đáy của kiến trúc, quản lý các trình điều khiển phần cứng (drivers) như bảo mật, quản lý bộ nhớ, quản lý tiến trình, mạng và nguồn điện. Nó đóng vai trò là lớp trung gian giữa phần cứng thiết bị và phần còn lại của Android.

**Hardware Abstraction Layer - HAL (Tầng trừu tượng phần cứng):**

- Cung cấp các standard interface (giao diện chuẩn) để che giấu các driver phần cứng bên dưới Linux Kernel, giúp các tầng cấp cao hơn gọi được các tính năng phần cứng (như camera, cảm biến, Bluetooth) mà không cần quan tâm đến chi tiết phần cứng của từng hãng điện thoại.

**Android Runtime - ART & Native C/C++ Libraries (Thư viện gốc & Máy ảo):**

- Native Libraries: Các thư viện viết bằng C/C++ (như SQLite cho cơ sở dữ liệu, OpenGL ES cho đồ họa 2D/3D, WebKit cho trình duyệt) được các thành phần khác sử dụng.

- Android Runtime (ART): Môi trường thực thi ứng dụng. Mỗi app chạy trong tiến trình riêng của nó với thực thể máy ảo riêng, biên dịch mã nguồn thành mã máy thông qua cơ chế AOT (Ahead-Of-Time) và JIT (Just-In-Time).

**Application Framework (Khung ứng dụng):**

Cung cấp toàn bộ các API bậc cao được các lập trình viên sử dụng hàng ngày để viết app (Activity Manager, Window Manager, Content Providers, View System, Notification Manager, Location Manager...).

**Applications (Tầng ứng dụng):**

Tầng cao nhất chứa các ứng dụng cài đặt sẵn trên máy (như Danh bạ, Tin nhắn, Trình duyệt) và các ứng dụng do người dùng tải về (Facebook, Zalo, Tiktok, app của bạn...).

## 2. Các Design Pattern phổ biến trong lập trình Android
Để tổ chức mã nguồn rõ ràng, dễ bảo trì và dễ viết kiểm thử (testing), các dự án Android hiện đại thường áp dụng các Architectural Design Patterns sau:

**a. MVVM (Model - View - ViewModel)**
Google khuyến nghị sử dụng MVVM kết hợp với các thành phần Android Jetpack (như ViewModel, LiveData / StateFlow).

View: Activity, Fragment hoặc Jetpack Compose UI. Chịu trách nhiệm hiển thị dữ liệu và nhận thao tác chạm từ người dùng, sau đó chuyển tiếp cho ViewModel.

ViewModel: Lưu trữ và quản lý dữ liệu liên quan đến giao diện (UI-related data) theo vòng đời vòng quay cấu hình (như xoay màn hình không bị mất dữ liệu). Nó không chứa code View trực tiếp nên rất dễ viết Unit Test.

Model: Tầng xử lý dữ liệu (Repositories, Local Database, Remote API).

**b. MVP (Model - View - Presenter)**
Rất phổ biến trong các dự án Android truyền thống trước khi MVVM ra đời.

Presenter: Đóng vai trò trung gian lấy dữ liệu từ Model, xử lý logic nghiệp vụ và ra lệnh cho View cập nhật giao diện thông qua Interface.

Nhược điểm: Presenter gắn chặt với Interface của View nên dễ gây ra tình trạng phình to code (Massive Presenter) và tốn bộ nhớ nếu không quản lý vòng đời cẩn thận.

**c. MVI (Model - View - Intent)**
Dựa trên nguyên lý lập trình hướng luồng dữ liệu một chiều (Unidirectional Data Flow - UDF) kết hợp với Jetpack Compose.

Intent: Đại diện cho các "hành động" hoặc "sự kiện" người dùng gây ra.

Model: Đại diện cho trạng thái (State) bất biến của màn hình. View chỉ việc render lại UI dựa hoàn toàn vào State đó. Giúp ứng dụng ít lỗi hơn và dễ kiểm soát trạng thái giao diện phức tạp.