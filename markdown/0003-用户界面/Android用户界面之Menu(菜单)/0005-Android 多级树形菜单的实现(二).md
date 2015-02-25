代码如下：
```  
@Override
public void onClick(View v) {
	adapter.RemoveAll();
	adapter.notifyDataSetChanged();
	superAdapter.RemoveAll();
	superAdapter.notifyDataSetChanged();
	if (v == btnNormal) {
		List<TreeViewAdapter.TreeNode> treeNode = adapter.GetTreeNode();
		for (int i = 0; i < groups.length; i++) {
			TreeViewAdapter.TreeNode node = new TreeViewAdapter.TreeNode();
			node.parent = groups[i];
			for (int ii = 0; ii < child[i].length; ii++) {
				node.childs.add(child[i][ii]);
			}
			treeNode.add(node);
		}
		adapter.UpdateTreeNode(treeNode);
		expandableList.setAdapter(adapter);
		expandableList.setOnChildClickListener(new OnChildClickListener() {
			@Override
			public boolean onChildClick(ExpandableListView arg0, View arg1,
					int parent, int children, long arg4) {
				String str = "parent id:" + String.valueOf(parent)
						+ ",children id:" + String.valueOf(children);
				Toast.makeText(testExpandableList.this, str, 300).show();
				return false;
			}
		});
	} else if (v == btnSuper) {
		List<SuperTreeViewAdapter.SuperTreeNode> superTreeNode = superAdapter
				.GetTreeNode();
		for (int i = 0; i < parent.length; i++) // 第一层
		{
			SuperTreeViewAdapter.SuperTreeNode superNode = new SuperTreeViewAdapter.SuperTreeNode();
			superNode.parent = parent[i];
			// 第二层
			for (int ii = 0; ii < child_grandson.length; ii++) {
				TreeViewAdapter.TreeNode node = new TreeViewAdapter.TreeNode();
				node.parent = child_grandson[ii][0][0]; // 第二级菜单的标题
				for (int iii = 0; iii < child_grandson[ii][1].length; iii++) // 第三级菜单
				{
					node.childs.add(child_grandson[ii][1][iii]);
				}
				superNode.childs.add(node);
			}
			superTreeNode.add(superNode);
		}
		superAdapter.UpdateTreeNode(superTreeNode);
		expandableList.setAdapter(superAdapter);
	}
}
[Tags]/**
 [Tags]* 三级树形菜单的事件不再可用，本函数由三级树形菜单的子项(二级菜单)进行回调
 [Tags]*/
OnChildClickListener stvClickEvent = new OnChildClickListener() {
	@Override
	public boolean onChildClick(ExpandableListView parent, View v,
			int groupPosition, int childPosition, long id) {
		String str = "parent id:" + String.valueOf(groupPosition)
				+ ",children id:" + String.valueOf(childPosition);
		Toast.makeText(testExpandableList.this, str, 300).show();
		return false;
	}
};
[Tags]/**
 [Tags]* 三级树形菜单的事件不再可用，本函数由三级树形菜单的子项(二级菜单)进行回调
 [Tags]*/
OnChildClickListener stvClickEvent = new OnChildClickListener() {
	@Override
	public boolean onChildClick(ExpandableListView parent, View v,
			int groupPosition, int childPosition, long id) {
		String str = "parent id:" + String.valueOf(groupPosition)
				+ ",children id:" + String.valueOf(childPosition);
		Toast.makeText(testExpandableList.this, str, 300).show();
		return false;
	}
};
```