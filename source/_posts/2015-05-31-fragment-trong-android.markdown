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