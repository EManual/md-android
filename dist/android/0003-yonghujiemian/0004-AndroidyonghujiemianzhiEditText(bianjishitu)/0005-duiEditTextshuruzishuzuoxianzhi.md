代码如下：
```  
private EditText mEditText;
mEditText = (EditText) findViewById(R.id.mEditText);
 /** 限制字数 */
mEditText.addTextChangedListener(new TextWatcher() {
	private CharSequence temp;
	private int selectionStart;
	private int selectionEnd;
	@Override
	public void beforeTextChanged(CharSequence s, int start, int count,
			int after) {
		temp = s;
	}
	@Override
	public void onTextChanged(CharSequence s, int start, int before,
			int count) {
	}
	@Override
	public void afterTextChanged(Editable s) {
		selectionStart = mEditText.getSelectionStart();
		selectionEnd = mEditText.getSelectionEnd();
		Log.d(TAG, "" + selectionStart);
		if (temp.length() > 8) {
			Toast.makeText(MAUpdateAty.this, "字数不能超过8个",
					Toast.LENGTH_SHORT).show();
			s.delete(selectionStart - 1, selectionEnd);
			int tempSelection = selectionStart;
			mEditText.setText(s);
			mEditText.setSelection(tempSelection);
		}
		Log.d(TAG, " " + selectionEnd);
	}
});
```