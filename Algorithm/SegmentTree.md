服了，因为今天早上的leetcode竞赛做了一道模版题，但因为不熟练还是超时没提交（核心还是上一道DP题一直错，情况考虑不清楚），所以晚上心血来潮说来几道。结果刚完成了一道，第二道就卡了一个多小时，到现在我也没看出哪错了。罢了，后面在看，现在要晕了。
趁现在还记得，把板子默写一下。BTW，我第一次学的时候，开的是4N的空间，父子关系和下标之间的联系也很清楚，今天写的2N空间的，光是理解父子关系的处理都用了一些时间。

```C++
// 一个求和的线段树模板
struct Node{
	int val;
	Node() : val(0) {}
	Node(int v) : val(v) {}

	static Node merge(const Node& l, const Node& r)
	{
		return Node(l.val + r.val);
	}
};

class SegmentTree
{
private:
	int n;
	vector<Node> tree;
	void build(const vector<int> &data)
	{
		n = data.size();
		tree.resize(n * 2);
		for (int i = 0; i < n; ++i)
		{
			tree[i + n] = Node(data[i]);
		}
		for (int i = n - 1; i > 0; --i)
		{
			tree[i] = Node::merge(tree[i * 2], tree[i * 2 + 1]);
		}
	}

public:
	SegmentTree(const vector<int> &data)
	{
		build(data);
	}

	void update(int index, int val)
	{
		int pos = index + n;
		tree[pos].val = val;
		while (pos > 1)
		{
			pos /= 2;
			tree[pos] = Node::merge(tree[pos * 2], tree[pos * 2 + 1]);
		}
	}

	int query(int left, int right)
	{
		Node node_left, node_right;
		left += n; right += n;
		while (left < right)
		{
			if (left % 2) {
				node_left = Node::merge(node_left, tree[left]);
				left++;
			}
			if (right % 2) {
				--right;
				node_right = Node::merge(node_right, tree[right];
			}
			left /= 2; right /= 2;
		}
		Node res = Node::merge(node_left, node_right);
		return res.val;
	}
};

```

