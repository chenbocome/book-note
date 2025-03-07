## 第8章 标准库容器
- `pair`和`tiple`
	- `make_pair`
	- `make_tuple`
- 容器
	- 顺序容器
		- `list`
		- `forward_list`
		- `vector`
		- `deque`
	- 关联容器
		- `map`
		- `multimap`
		- `set`
		- `multiset`
	- 专用容器
		- `queue`
		- `priority_queue`
	- 迭代器
		- `next`
		- `advance`
		- `prev`
		- `begin`
		- `cbegin`
		- `crbegin`
- 算法
	- 标准库在头文件`<algorithm>`中包含大量的通用函数。
	- 元素迭代
		- `fill`函数将使用一个值填充一个容器
		- `generate`函数时类似的，但不是单个值，它可以是一个函数、一个函数对象或者`lambda`表达式。
		- `for_each`函数将变量访问迭代器指定的所有元素，间接引用迭代器，并将元素传递给函数。
		- `transform`
	- 获取信息
		- `count`函数用于对区间中指定数量的数字项进行统计。
		- `count_if`
		- `all_of`
		- `any_of`
		- `none_of`
	- 容器比较
		- `equal`
		- `mismach`
		- `is_permutation`
	- 修改元素
		- `reverse`翻转
		- `copy`
		- `copy_backward`
		- `reverse_copy`
		- `move`
		- `move_backward`
		- `remove_copy`
		- `remove_copy_if`
		- `erase`
		- `replace`
		- `replace_if`
		- `replace_copy`
		- `replace_copy_if`
		- `rorate`
		- `unique`
		- `unique_copy`
		- `iter_swap`
		- `swap_ranges`
	- 查找元素
		- `min_element`
		- `max_element`
		- `minmax_element`
		- `adjacent_find`
		- `find`
		- `find_if`
		- `find_if_not`
		- `different`
		- `find_first_of`
		- `search`
		- `find_end`
	- 元素排序
		- `sort`
		- `stable_sort`
		- `partial_sort_copy`
		- `is_sorted`
		- `is_sorted_until`
		- `nth_element`
		- `partial_sort`
		- `partition`
		- `shuffle`
		- `heap`
		- `lower_bound`
		- `upper_bound`
		- `merge`
		- `includes`
		- `set_union`
		- `final`
- 数值库
	- 使用`<ratio>`的编译器运算。
	- 使用`<complex>`的复数运算。
- 标准库应用

		#include <iostream>
		#include <fstream>
		#include <vector>
		#include <string>
		#include <list>
		
		using namespace std;
		using vec_str = vector<string>;
		using list_str = list<string>;
		using vec_list = vector<list_str>;
		
		void usage()
		{
			cout << "usage: csv_parser file" << endl;
		}
		
		list_str parse_line(const string& line)
		{
			list_str data;
			string::const_iterator it = line.begin();
			string item;
			bool bQuote = false;
			bool bDQuote = false;
			do {
				switch (*it) {
				case '\'':
					if (bDQuote) {
						item.push_back(*it);
					}
					else {
						if ((it + 1) != line.end() && *(it + 1) == '\'') {
							item.push_back(*it);
							++it;
						}
						else {
							bQuote = !bQuote;
							if (bQuote) item.clear();
						}
		
					}
					break;
				case '"':
					if (bDQuote) {
						item.push_back(*it);
					}
					else {
						if ((it + 1) != line.end() && *(it + 1) == '"') {
							item.push_back(*it);
							++it;
						}
						else {
							bQuote = !bQuote;
							if (bQuote) item.clear();
						}
		
					}
					break;
				case ',':
					if (bQuote || bDQuote)
						item.push_back(*it);
					else
						data.push_back(move(item));
						break;
					default:
						item.push_back(*it);
				}
				++it;
			} while (it != line.end());
			//由于item变量即将被销毁，所以对move的调用可以确保其内容被移动到列表中，而不是赋值。
			//如果没有这个调用，当将元素放入列表时，字符的拷贝构造函数将被调用。
			data.push_back(move(item));
		
			return data;
		}
		
		int main(int argc, const char* argv[])
		{
			if (argc <= 1)
			{
				usage();
				return 1;
			}
		
			ifstream stm;
			stm.open(argv[1], ios_base::in);
			if (!stm.is_open())
			{
				usage();
				cout << "cannot open" << argv[1] << endl;
				return 1;
			}
		
			vec_str lines;
			for (string line; getline(stm, line);)
			{
				if (line.empty()) continue;
				lines.push_back(move(line));
			}
			stm.close();
		
			vec_list parsed;
			for (string& line : lines)
			{
				parsed.push_back(parse_line(line));
			}
		
			int count = 0;
			for (list_str row : parsed)
			{
				cout << ++count << "> ";
				for (string field : row)
				{
					cout << field << " ";
				}
				cout << endl;
			}
			return 0;
		}