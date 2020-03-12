---
layout: post
title: Android Errors
tags: Android
categories: Android
---
Android常见报错

* TOC 
{:toc}

### Q1:Fragment中打开新页面出现报错：Attempt to invoke virtual method 'java.lang.String android.content.Context.getPackageName()' on a null object reference

一般情况下需要在Fragment的onAttach中获取context，而不是到处使用getActivity获取context：

```java
@Override
    public void onAttach(Context context) {
        super.onAttach(context);
        mActivity = (Activity) context;
    }
```

```java
Intent intent = new Intent(mActivity, WebActivity.class);
                intent.putExtra(WebActivity.TARGET_URL, NetContents.PAR_VIEW_HOST);
                intent.putExtra(WebActivity.WEB_TYPE_KEY, WebActivity.WEB_TO_PAR);
                intent.putExtra(NearbyFragment.LATITUDE_KEY, latitude + "");
                intent.putExtra(NearbyFragment.LONGITUDE_KEY, longitude + "");
                intent.putExtra("title", houseDetailBo.getEstateName());
                intent.putExtra(WebActivity.WEB_SHARE_KEY, false);
                startActivity(intent);
```

### Q2:关于WebView中js方法调用不到的问题

一般情况下，在测试包中能够调用成功但是在正式包中无法调用的情况都和代码混淆有关。

常见会受代码混淆影响的代码：

```java
web_view.evaluateJavascript("getAppShareData()", value -> {
//                        Logger.i("buobao11:"+value);
                    if (value.startsWith("\"")) {
                        value = StringEscapeUtils.unescapeJavaScript(value.substring(1, value.length() - 1));
                    }
//                        Logger.i(value);
                    try {
                        Gson gson = new Gson();
                        HousePostBean postBean = gson.fromJson(value, HousePostBean.class);
                        shareImage = postBean.getDefaultImage();
                        shareSumtitle = postBean.getSubtitle();
                        title = postBean.getTitle();
                        sharebody = postBean.getSharebody();

                    } catch (Exception e) {

                    } finally {
                        MyUtils.copyText2Clip(this,sharebody);
                        showShareDialog();
                    }
                });
```

以上会调用不到getAppShareData()方法。

```java
web_view.addJavascriptInterface(new MyJavaScriptInterface(), "findProperty");
```

所有findProperty域下的js方法都会有影响。

解决的方法很简单，去除这些调用处的代码混淆。

