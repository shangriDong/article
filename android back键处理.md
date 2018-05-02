首先重写该方法，拦截KeyEvent.KEYCODE_BACK 键 。

	public boolean onKeyDown(int keyCode, KeyEvent event) {
	    if (keyCode == KeyEvent.KEYCODE_BACK) {
	        if (!mRechargeFragment.onBackPressed()) {
	            return super.onKeyDown(keyCode, event);
	        } else {
	            return false;
	        }
	    } else {
	        return super.onKeyDown(keyCode, event);
	    }
	}


在fragment中加入该方法，
mRechargeFragment.onBackPressed()

	public boolean onBackPressed() {
		//加入你想要的逻辑，并返回适当的值 true消费该事件，false继续传递
        if (mLoginPopupWindow.isShowing()) {
            mLoginPopupWindow.dismiss();
            return true;
        } else {
            return false;
        }
    }