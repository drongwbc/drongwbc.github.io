## 1、子集和问题

给定n长度的集合，找到一个元素和为c的子集

属于子集回溯，剪枝条件为当前和加上所有待选取的数是否小于等于c

```c++
#include <iostream>
#include <vector>

using namespace std;
constexpr int MAX_SIZE = 1001;

int n, c;
int arr[MAX_SIZE];
int suffic_sum[MAX_SIZE];//后缀和

void dfs(int id, vector<int> &ve, int sum) {
    if(sum == c) {
        for(int it : ve) {
            cout << it << " ";
        }
        cout << endl;
        return;
    }
    if(sum > c || id > n) {
        return;
    }
    if(sum + suffic_sum[id] >= c) {//有可能产生解
        ve.push_back(arr[id]);
        dfs(id + 1, ve, sum + arr[id]);//选取该数
        ve.pop_back();
        if(sum + suffic_sum[id] > c) {//后面所有的数加起来大于c，该数可以不选
            dfs(id + 1, ve, sum);//不选该数
        }
    }
}

int main() {
    //5 10 2 2 6 5 4
    cin >> n >> c;
    for(int i = 1; i <= n; i++) {
        cin >> arr[i];
    }
    for(int i = n; i >= 1; i--) {
        suffic_sum[i] = arr[i] + suffic_sum[i + 1];
    }
    vector<int> ve;
    dfs(1, ve, 0);
    return 0;
}
```

## 2、最小重量机器设计问题

给定n个部件的在m个供应商处的不同重量和价格，计算总价格小于等于d时的最小重量

属于全排列回溯，共有m<sup>n</sup>种排列，剪枝条件为当前总重量加上待选取重量后是否小于已经求得的最小重量

```c++
#include <iostream>
#include <vector>

using namespace std;
constexpr int MAX_SIZE = 1001;

int n, m, d; //总价格不超过d
int price[MAX_SIZE][MAX_SIZE];//价格
int weight[MAX_SIZE][MAX_SIZE];//重量，初始为均使用第一个供应商
int ret;//总价格不超过d的最小重量
vector<int> ve;

void dfs(int id, int sum_price, int sum_weight, vector<int> &ve) {
    if(id > n) { //到了尽头
        if(sum_weight < ret) { //可更新
            ret = sum_weight;
            ::ve = ve;
        }
        return;
    }
    for(int i = 1; i <= m; i++) {
        //价格小于等于d，总重量小于等于当前最优解
        if(sum_price + price[id][i] <= d && sum_weight + weight[id][i] <= ret) {
            ve.push_back(i);
            dfs(id + 1, sum_price + price[id][i], sum_weight + weight[id][i], ve);
            ve.pop_back();
        }
    }
}

int main() {
    //3 3 4 1 2 3 3 2 1 2 2 2 1 2 3 3 2 1 2 2 2
    cin >> n >> m >> d;
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= m; j++) {
            cin >> price[i][j];
        }
    }
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= m; j++) {
            cin >> weight[i][j];
        }
        ret+=weight[i][1];
    }
    vector<int> t;
    dfs(1, 0, 0, t);
    cout << ret << endl;
    for(int it :ve) {
        cout << it << " ";
    }
    cout << endl;
    return 0;
}
```

## 3、工作分配问题

n件工作分配n个人，每个人完成同一件工作所需时间不同，计算最小时间

属于全排列回溯，共有n!种排列，剪枝条件为当前总时间加上待选取的时间是否小于已经求得的最小解

```c++
#include <iostream>
#include <vector>
#include <climits>

using namespace std;
constexpr int MAX_SIZE = 1001;

int n;
int arr[MAX_SIZE][MAX_SIZE];
int ret;

bool book[MAX_SIZE];//该人是否已分配工作
void dfs(int id, int sum) {
    if(id > n) {
        if(sum < ret) {
            ret = sum;
        }
        return;
    }
    for(int i = 1; i <= n; i++) {
        if(!book[i] && sum + arr[i][id] <= ret) {//还有可能产生更优解
            book[i] = true;
            dfs(id + 1, sum + arr[i][id]);
            book[i] = false;
        }
    }
}

int main() {
    //3 10 2 3 2 3 4 3 4 5
    cin >> n;
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            cin >> arr[i][j];
            ret += arr[i][j];
        }
    }
    dfs(1, 0);
    cout << ret << endl;
    return 0;
}
```

## 4、最佳调度问题

n个需要任务不同完成时间的任务，k机器用于工作，机器间可并行，计算最快完成时间

属于全排列回溯，共有n<sup>k</sup>种排列，剪枝条件为当前任务交给待选取的机器后，完成时间是否比已经求得的最小时间小

```c++
#include <iostream>
#include <numeric>
#include <climits>
#include <vector>

using namespace std;
constexpr int MAX_SIZE = 1001;

int n, k;
int arr[MAX_SIZE];
int ret = INT_MAX / 2;

void dfs(int id, int *book, int sum) {
    if(id > n) {
        if(sum < ret) {
            ret = sum;
        }
        return;
    }
    for(int i = 1; i <= k; i++) {
        int now_sum = max(sum, book[i] + arr[id]);
        if(now_sum < ret) {//还有可能更优
            book[i] += arr[id];
            dfs(id + 1, book, now_sum);
            book[i] -= arr[id];
        }
    }
}

int main() {
    //7 3 2 14 4 16 6 5 3
    cin >> n >> k;
    for(int i = 1; i <= n; i++) {
        cin >> arr[i];
    }
    int book[k + 1] {0};
    dfs(1, book, 0);
    cout << ret << endl;
    return 0;
}
```

