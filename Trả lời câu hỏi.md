**Trả lời câu hỏi**

**Câu 1: Vòng đời của Fragment khi quay ngang màn hình, dữ liệu có mất hay không?**



\- Có, dữ liệu sẽ bị mất khi quay ngang màn hình.

Fragment sẽ chạy các hàm onPause, onStop, onDestroyView, onDestroy, onDetach. Sau đó Fragment sẽ chạy lại các hàm onAttach, onCreate, onCreateView, onViewCreated, onStart, onResume.



Cách giải quyết:

1\. Sử dụng ViewModel để lưu trữ dữ liệu: Sẽ không bị mất khi quay màn hình



2\. Sử dụng hàm onSaveInstanceState() để lưu trữ bằng bundle. Nhược điểm là sẽ chỉ lưu được 1 lượng thông tin khá nhỏ



3\. Lưu dữ liệu vào các bộ nhớ như SharedPreferences, hoặc RoomDatabase. Nhược điểm là dùng các bộ nhớ đấy thì thường sẽ để lưu cài đặt người dùng chứ k phải để lưu trạng thái UI -> Dễ làm giảm hiệu năng ứng dụng





**Câu 2: Ngoài kiến trúc MVVM dùng Viewmodel thì còn cách nào để ko mất dữ liệu khi fragment quay ngang không**



Có: Chúng ta có thể sử dụng hàm onSaveInstanceState để lưu trữ dữ liệu trong bundle và lấy nó ra ở hàm onViewCreated (Kiểm tra xem biến saveInstanceState có null hay không?, nếu ko nul thì lấy dữ liệu rồi gán lại để dữ liệu hiển thị)



Hoặc cách khác là lưu dữ liệu vào các bộ nhớ như SharedPreferences hoặc Room DB





**Câu 3: Lướt video có độ trễ, muốn lướt phát xem luôn như tiktok (Cũng dùng ExoPlayer)**



Nguồn (Tìm hiểu):



Sử dụng Caching (lưu bộ đệm) và dùng Pre-loading/Pre-buffering (tải trước)



Khởi tạo 1 instance của SimpleCache (khởi tạo trong lớp Application)



Pre-loading/Pre-buffering: Khi người dùng đang xem, cta âm thầm chuẩn bị sẵn player cho video ở vị trí n+1 (có thể thêm ở vị trí n-1).



Cách sử dụng: Dùng ViewPager2.OnPageChangeCallback()



1.Trong Adapter của viewpager2, mỗi ViewHolder sẽ giữ 1 Instance của Exoplayer



2\. Trong Fragment/Activity chứa ViewPager2, đăng ký 1 onPageChangeCallback



3\. dùng viewpager2.registerOnPageChangeCallback -> Sau đó truyền vào 1 đối tượng thuộc lớp ViewPager2.onPageChangeCallback



4\. Lưu vị trí hiện tại: currentposition = 0



5\. Override lại method onPageSelected



 -> Dừng video ở trang cũ

 -> val oldViewHolder = (viewPager.findViewHolderForAdapterPosition(currentPosition)) as? VideoViewHolder)



 -> oldViewHolder?.stopVideo()



6\. Chuẩn bị phát video ở trang mới

 -> val newViewHolder = (viewPager.findViewHolderForAdapterPosition(currentPosition)) as? VideoViewHolder)



 -> newViewHolder?.playVideo()



7\. Chuẩn bị cho video tiếp theo



 -> val nextViewHolder = (viewPager.findViewHolderForAdapterPosition(currentPosition + 1)) as VideoViewHolder)

 -> nextViewHolder?.prepareVideo()





**Câu 4: Cách hoạt động của Glide**



**Cú pháp:**



Glide.with(context)

 	.load("URL\_cua\_anh)

 	.placeholder("URL\_place\_holder")

 	.into(image\_view)



1. Glide sẽ kiểm tra Memory Cache (bộ nhớ đệm RAM)

 -> Nếu đã có, glide sẽ hiển thị ảnh lên imageview ngay lập tức



2\. Kiểm tra Disk Cache (Bộ nhớ đệm)

-> Nếu không tìm thấy ở Ram, Glide sẽ kiểm tra bộ nhớ trên đĩa của thiết bị. Ở đây có 2 cấp độ

 	- Resource cache: Kiểm tra ảnh đã được xử lý (resize, crop) có tồn tại trên đĩa không. Nếu có sẽ giải mã ảnh và đưa lên Ram để hiển thị

 	- Source cache: Nêu skhoong có, kiểm tra ảnh gốc có tồn tại trên đĩa khoogn. Nếu có, bỏ qua bước tải vè đến bước tiếp theo luôn (Bước 4)



3\. Tải DL mới: Nếu ảnh k tồn tại ở cache nào, Glide sẽ tải dl từ nguồn cung cấp. Sau khi tải xong thì dữ liệu gốc sẽ được lưu ở Source cache



4\. Giả mã \& biến đổi: Dữ liệu thô sẽ được biến đổi thành 1 đối tượng Bitmap. Sau đó, glide áp dụng các biến đổi đã cài (crop, resize..). Cuối cùng sẽ lưu vào Resource Cache



5\. Hiển thị và lưu vào Memory Cache (Ram)



**Về vòng đời Glide:**



Khi onResume() hoặc onStart() được gọi: Sẽ bắt đầu tải ảnh



Khi onPause() hoặc onStop() được gọi: Ngừng việc tải ảnh



Khi onDDestroy được gọi: Sẽ tự động hủy bỏ gọi và tất cả tài nguyên được dọn dẹp





**Câu 5: Xử lý thêm nút X để xóa bài viết trong List**



Nếu có sử dụng api thì sẽ call API xóa 1 bài viết ở Danh sách



Nếu k sử dụng api mà sử dụng nguồn dữ liệu cục bộ  (lưu trữ bằng Room Database) -> Khi bấm nút X -> Hiển thị 1 dialog xác nhận xóa. Nếu xác nhận xóa oke thì bài viết sẽ bị xóa khỏi DB.



Nhờ vào LiveData hoặc StateFlow thì danh sashc bài viết sẽ được cập nhật tự động





**Câu 6: Xử lý Refresh Data**



**Cách 1:**

Sử dụng thư viện SwipeRefreshLayout



* Bọc Activity/Fragment chính bằng 1 thẻ SwipeRefreshLayout (Nó giống như các thẻ bth thôi, có id, width, height
* Đăng ký sử dụng hàm setOnReFreshListener() -> truyền dạng lambda

 	Sau đó thì call api lấy tất cả dữ liệu lại. Sau đó sử dụng hàm setRefreshing (false) -> để ngừng refresh sau 1 lần refresh.



**Cách 2:**



Dùng Fush Notification



Server: Khi 1 người dùng tạo bài viết mới, server sẽ gửi 1 "silent push notification" qua Firebase Cloud Messaging (FCM) đến các client



Client: Ứng dùngj của bạn nhận được thông báo trong FirebaseMessagingService



Hành động: Nếu ứng dụng đang mở, hiển thị 1 thông báo : Có bài viết mới để xem hoặc tự động call api tải bài viết

 	- Nếu ứng dụng đang đóng, hiển thị 1 thông báo đẩy bth



**Cách 3: Dùng WebSockets**



Chưa rõ lắm vì em chưa tiếp cận cái này bao giờ ạ





**Câu 7:**



Coroutine là 1 lightweight threads, 1 tiến trình siêu nhẹ chạy bên trong 1 thread. Cho phép viết code bất đồng bộ 1 cách tuần tự và dễ đọc. Nó xử lý các vụ đợi (như đợi mạng, đợi đọc file) mà không cần chặn thread.



Coroutine rất nhẹ, việc tạo ra 1 coroutine hầy như ko tốn chi phí

Coroutine tự nhận biết vòng đời, tự hủy khi view bị hủy đi -> tránh tốn bộ nhớ

Coroutine giúp đỡ viết code lồng nhau như khi sử dụng Thread hay CallBack



Khi cần sử dụng các tác vụ nặng thì vẫn ưu tiên Thread hơn

