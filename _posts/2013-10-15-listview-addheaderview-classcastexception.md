---
layout: post
title: "ListView addHeaderView报ClassCastException"
description: "ListView addHeaderView报ClassCastException"
category: "android"
tags: [android, listview]
---
{% include JB/setup %}

在使用ListView的时候，需要添加一个header，结果报异常了。
代码如下：

	mHeaderView = inflater.inflate(R.layout.test_header, container, false);
	mListView.addHeaderView(mHeaderView);
执行后报错，ClassCastException。

	java.lang.ClassCastException: android.widget.LinearLayout$LayoutParams
	at android.widget.ListView.clearRecycledState(ListView.java:582)
	at android.widget.ListView.resetList(ListView.java:568)
	at android.widget.ListView.setAdapter(ListView.java:500)

栈信息很明确是clearRecycledState抛出来的，再来看代码。

    void resetList() {
        // The parent's resetList() will remove all views from the layout so we need to
        // cleanup the state of our footers and headers
        clearRecycledState(mHeaderViewInfos);
        clearRecycledState(mFooterViewInfos);

        super.resetList();

        mLayoutMode = LAYOUT_NORMAL;
    }

    private void clearRecycledState(ArrayList<FixedViewInfo> infos) {
        if (infos != null) {
            final int count = infos.size();

            for (int i = 0; i < count; i++) {
                final View child = infos.get(i).view;
                final LayoutParams p = (LayoutParams) child.getLayoutParams();
                if (p != null) {
                    p.recycledHeaderFooter = false;
                }
            }
        }
    }

clearRecycledState中要求headerview的LayoutParams要强转为android.widget.AbsListView$LayoutParams类型。
而我们的mHeaderView是用LayoutInflater初始化的。

查看方法public View inflate(int resource, ViewGroup root, boolean attachToRoot)的说明。

    /**
     * Inflate a new view hierarchy from the specified xml resource. Throws
     * {@link InflateException} if there is an error.
     * 
     * @param resource ID for an XML layout resource to load (e.g.,
     *        <code>R.layout.main_page</code>)
     * @param root Optional view to be the parent of the generated hierarchy (if
     *        <em>attachToRoot</em> is true), or else simply an object that
     *        provides a set of LayoutParams values for root of the returned
     *        hierarchy (if <em>attachToRoot</em> is false.)
     * @param attachToRoot Whether the inflated hierarchy should be attached to
     *        the root parameter? If false, root is only used to create the
     *        correct subclass of LayoutParams for the root view in the XML.
     * @return The root View of the inflated hierarchy. If root was supplied and
     *         attachToRoot is true, this is root; otherwise it is the root of
     *         the inflated XML file.
     */
    public View inflate(int resource, ViewGroup root, boolean attachToRoot) {

由此可以看出，由于第二个参数我们给的是一个具体的layout，第三个参数给的是false，所以inflater按照第二个参数的layout给初始化了。
修改代码为：

        mHeaderView = inflater.inflate(R.layout.hotshare_huati_tab, null);
即可。
