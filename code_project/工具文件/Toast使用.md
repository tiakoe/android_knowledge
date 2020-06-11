



```java
@SuppressLint("ShowToast")
private static void createToast(String msg) {
    if (toast == null) {
        toast = Toast.makeText(XUtil.getApplication(), msg, Toast.LENGTH_SHORT);
    } else {
        toast.setText(msg);
    }
    LinearLayout linearLayout = (LinearLayout) toast.getView();
    TextView messageTextView = (TextView) linearLayout.getChildAt(0);
    messageTextView.setTextSize(15.0F);
    toast.show();
}

public static void showToast(final String msg) {
    if ("main".equals(Thread.currentThread().getName())) {
        createToast(msg);
    } else {
        ActivityUtil.getCurrentActivity().runOnUiThread(() -> ToastUtil.createToast(msg));
    }

}
```



```java
    private static void createCenterToast(String msg) {
        if (toast == null) {
            toast = Toast.makeText(XUtil.getApplication(), msg, Toast.LENGTH_SHORT);
        } else {
            toast.setText(msg);
        }

        LinearLayout linearLayout = (LinearLayout) toast.getView();
        TextView messageTextView = (TextView) linearLayout.getChildAt(0);
        toast.setGravity(17, 0, 0);
        messageTextView.setTextSize(15.0F);
        toast.show();
    }

    public static void cancelToast() {
        if (toast != null) {
            toast.cancel();
        }
    }
```

