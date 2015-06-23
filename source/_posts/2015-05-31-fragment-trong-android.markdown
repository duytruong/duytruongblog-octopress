---
layout: post
title: "Fragment trong Android"
date: 2015-05-31 13:19
comments: true
categories:
- android
---

## Giới thiệu ##

Fragment là một bộ phận trong giao diện của ứng dụng, nó có thể được đặt vào trong một Activity.
Fragment cho phép bạn có thể tách Activity thành các phần riêng biệt có khả năng tái sử dụng, chúng có 
chu trình sống (lifecycle) và giao diện riêng của mình. Sử dụng fragment giúp bạn tạo ra giao diện động và mềm dẻo
tương thích với nhiều kích cỡ màn hình khác nhau, có thể thêm, xóa, thay đổi các fragment với nhau trong khi Activity
chứa chúng đang được chạy. Nôm na là chỉ với một màn hình (activity), bạn có thể đặt nhiều giao diện khác nhau trên đó.

## Tạo mới một fragment ##

Để tạo mới một fragment, bạn phải tạo một class thừa kế class `Fragment`, sau đó thiết kế giao diện cho nó (cái này tùy, bạn có thể
tạo một fragment mà không có giao diện để thực hiện các tác vụ chạy nền, phần này sẽ bàn sau), và cài đặt các chức năng mà fragment xử lý.
Nếu fragment của bạn có giao diện, override hàm onCreateView để tạo và trả về giao diện mà bạn muốn.

```java
package duy.fragments;

import android.app.Activity;
import android.app.Fragment;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class SkeletonFragment extends Fragment {
	
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container,
			Bundle savedInstanceState) {
				
		return inflater.inflate(R.layout.skeleton_fragment, container, false);
	}
}
```

Fragment không cần phải đăng ký trong manifest, lý do đơn giản là nó chỉ có thể tồn tại khi được gắn vào một activity nào đó và vòng đời của nó phụ thuộc vào vòng đời của activity.

## Vòng đời của một Fragment ##

Cơ bản mà nói thì vòng của một fragment giống rất nhiều với vòng đời của một activity, cũng có các sự kiện (lifecycle events) created, started, resumed, paused, stopped và destroyed.
Ngoài ra fragment còn có các hàm callback xử lý các sự kiện riêng của nó như:

* `onAttach()` - được gọi khi fragment đã được liên kết với activity

* `onCreateView()` - được gọi để tạo ra cấu trúc view được liên kết với fragment

* `onActivityCreated()` - được gọi khi activity chứa fragment và giao diện của fragment đã được tạo xong (onCreate() của activity đã return về)

* `onDestroyView()` - được gọi khi cấu trúc view được liên kết với fragment đang bị gỡ bỏ

* `onDetach()` - được gọi khi fragment đang bị tách ra khỏi activity

Đoạn code dưới đây mô tả các hành động nên làm ở mỗi sự kiện:

```java
package dauruy.fragments;

import android.app.Activity;
import android.app.Fragment;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Toast;

public class SkeletonFragment extends Fragment {

    // được gọi khi fragment đã được liên kết với activity
	@Override
	public void onAttach(Activity activity) {
		super.onAttach(activity);
		// lấy liên kết đến activity chứa fragment này
	}

	// thực hiện các bước khởi tạo ban đầu cho fragment
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// khởi tạo
	}
	
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container,
			Bundle savedInstanceState) {
		return inflater.inflate(R.layout.skeleton_fragment, container, false);
	}
	
	@Override
	public void onActivityCreated(Bundle savedInstanceState) {
		super.onActivityCreated(savedInstanceState);
		// hoàn thành việc khởi tạo fragment
	}

	// được gọi vào lúc bắt đầu của visible lifetime (khoảng thời gian nhìn thấy được fragment)
	@Override
	public void onStart() {
		super.onStart();
		// thực hiện thay đổi UI của fragment
	}

	// được gọi vào lúc bắt đầu của active lifetime
	@Override
	public void onResume() {
		super.onResume();
		// tiếp tục cập nhật UI, chạy tiếp thread, xử lý các tác vụ bị tạm hoãn của fragment
	}

	// được gọi để lưu lại tráng thái của UI ở thời điểm kết thúc của active lifecycle
	@Override
	public void onSaveInstanceState(Bundle outState) {
		super.onSaveInstanceState(outState);
		// lưu trạng thái của UI vào outState.
		// bundle này sẽ được truyền tới onCreate, onCreateView và onActivityCreated
		// nếu như activity chứa fragment bị kết thúc và khởi động lại.
	}

	// được gọi vào lúc cuối của active lifetime
	@Override
	public void onPause() {
		super.onPause();
		// hoãn việc cập nhật UI, thread, các tiến trình đòi hỏi tài nguyên CPU cao (không cần update trong lúc inactive).
		// lưu lại các thay đổi, trạng thái của fragment.
	}

	// được gọi vào lúc cuối của visible lifetime
	@Override
	public void onStop() {
		super.onStop();
		// hoãn các cập nhật UI còn sót lại, thread, hoặc các xử lý không cần thiết lúc fragment không visible.
	}

	// được gọi khi các view của fragment đã bị tách ra
	@Override
	public void onDestroyView() {
		super.onDestroyView();
		// thu hồi tài nguyên các view đã sử dụng
	}

	// được gọi vào lúc cuối của full lifetime
	@Override
	public void onDestroy() {
		super.onDestroy();
		// thu hồi tài nguyên, kết thúc các thread, đóng kết nối tới database...
	}

	@Override
	public void onDetach() {
		super.onDetach();
	}
}
```

## Các lifecycle event đặc trưng của Fragment ##

### Gắn vào và tách ra khỏi Activity (attaching và detaching) ###

Toàn bộ thời gian sống của Fragment được tính từ khi nó bắt đầu gắn vào Activity (`onAttach` được gọi) và kết thúc khi tách ra khỏi Activity (`onDetach` được gọi).
Lưu ý:

* Khi Fragment/Activity ở trạng thái paused, `onDetach` có thể không được gọi do tiến trình của Activity đã bị kết thúc giữa chừng trong khi Fragment/Activity vẫn chưa hoàn thành toàn bộ lifecycle của nó.

* `onAttach` được gọi trước khi UI của Fragment được tạo ra, trước cả Fragment hoặc Activity hoàn tất quá trình khởi tạo của chúng.

### Tạo và hủy Fragment (creating và destroying) ###

Giai đoạn tạo lập của Fragment (created lifetime) nằm giữa lần gọi đầu tiên tới `onCreate` và lần gọi cuối cùng tới `onDestroy`.
`onDestroy` cũng có thể không được gọi khi tiến trình của Activity bị ngưng đột ngột. Do đó việc thu hồi tài nguyên có thể không được thực hiện nếu ta chỉ phụ thuộc vào `onDestroy`

Các công việc khởi tạo Fragment nên được làm trong `onCreate`. Nếu bạn muốn tạo ra các đối tượng có phạm vi trong toàn bộ class (class scoped object) thì nên làm ở đây vì
`onCreate` chỉ được gọi một lần duy nhất trong vòng đời của Fragment.

### Tạo và hủy giao diện ###

Giao diện của một Fragment được khởi tạo và hủy khi `onCreateView` và `onDestroyView` được gọi tương ứng.
Nếu Fragment muốn tương tác giao diện của Activity chứa nó, bạn phải đợi `onActivityCreated` được gọi, lúc đó Activity đã được khởi tạo hoàn tất và toàn bộ giao diện của nó đã được tạo xong.

## Tìm hiểu về Fragment Manager ##