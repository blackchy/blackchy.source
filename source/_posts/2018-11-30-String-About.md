---
title: String 相关
date: 2018-11-30 00:00:01
categories: android
tags: string
---

Android中String相关的操作，把它们记录下来。
<!-- more -->

### 修改部分字体大小

原字体：<font size=4>字体大小改变</font> &nbsp;&nbsp;修改后：<font size=4>字体大小改</font> <font size=2>变</font>
```
CharSequence charSequence1 = "字体改变大小";
SpannableString spannableString = new SpannableString(charSequence1);
  spannableString.setSpan(new RelativeSizeSpan(0.5f), charSequence1.length() - 1,
  charSequence1.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
tvTitle.setText(spannableString);
```

### 修改部分字体颜色

原字体：<font size=4>字体改变颜色</font> &nbsp;&nbsp;修改后：<font size=4>字体改变颜</font> <font size=4 color=#FFB72B>色</font>
```
CharSequence charSequence = "字体改变颜色";
SpannableStringBuilder spannableStringBuilder = new SpannableStringBuilder(charSequence);
spannableStringBuilder.setSpan(
  new ForegroundColorSpan(getResources().getColor(R.color.gl_vip_up)),
  spannableStringBuilder.length() - 1, spannableStringBuilder.length(),
  Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
tvTitle.setText(spannableStringBuilder);
```

注：待补充