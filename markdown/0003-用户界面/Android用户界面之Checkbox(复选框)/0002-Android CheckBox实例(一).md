代码如下：
```  
list = new OnKeyListener() {
	@Override
	public boolean onKey(View v, int keyCode, KeyEvent event) {
		if (mBox1.isChecked()) {
			mBox1.setChecked(false);
		}
		if (mBox2.isChecked()) {
			mBox2.setChecked(false);
		}
		if (mBox3.isChecked()) {
			mBox3.setChecked(false);
		}
		if (mBox4.isChecked()) {
			mBox4.setChecked(false);
		}
		return false;
	}
};
mEditText.setOnKeyListener(list);
mEditText1.setOnKeyListener(list);
listner = new OnCheckedChangeListener() {
	@Override
	public void onCheckedChanged(CompoundButton buttonView,
			boolean isChecked) {
		switch (buttonView.getId()) {
		case R.id.Plus:
			if (!isEmpty(mEditText, mEditText1)) {

				Confirm();
				mBox1.setChecked(false);
				return;
			}
			break;
		case R.id.Cut:

			if (!isEmpty(mEditText, mEditText1)) {
				Confirm();
				mBox2.setChecked(false);
				return;
			}
			break;
		case R.id.Ride:
			if (!isEmpty(mEditText, mEditText1)) {
				Confirm();
				mBox3.setChecked(false);
				return;
			}
			break;
		case R.id.Except:
			if (!isEmpty(mEditText, mEditText1)) {
				Confirm();
				mBox4.setChecked(false);
				return;
			}
			break;
		default:
			break;
		}
		if (mBox1.isChecked()) {
			mTextView.setText(GetOperation("+"));
		} else {
			mTextView.setText("");
		}
		if (mBox2.isChecked()) {
			mTextView2.setText(GetOperation("-"));
		} else {
			mTextView2.setText("");
		}
		if (mBox3.isChecked()) {
			mTextView3.setText(GetOperation("*"));
		} else {
			mTextView3.setText("");
		}
		if (mBox4.isChecked()) {
			mTextView4.setText(GetOperation("/"));
		} else {
			mTextView4.setText("");
		}
	}
};
mBox1.setOnCheckedChangeListener(listner);
mBox2.setOnCheckedChangeListener(listner);
mBox3.setOnCheckedChangeListener(listner);
mBox4.setOnCheckedChangeListener(listner);
```