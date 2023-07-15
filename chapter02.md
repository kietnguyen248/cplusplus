# Chapter 02 - BIẾN CON TRỎ VÀ KỸ THUẬT BỘ NHỚ ĐỘNG

Kỹ thuật bộ nhớ động - thành phần dữ liệu có kích thước thay đổi trong quá trình chương trình đang chạy. Kỹ thuật quan trọng, không thể thiếu đối với lập trình viên chuyên nghiệp.

## I. Mảng động trong thư viện chuẩn

STL - Standard Template Library - thư viện chuẩn, cung cấp sẳn kiểu vector<T> cho phép khai báo & sử dụng các mảng có kích thước thay đổi.

### 1.1 Kiểu vector<T> trong STL của C++

vector<T> : mảng gồm các p.tử kiểu T.

```C++
void arrayOutput(vector<float> &a) {
  for (int i = 0; i < a.size(); i++) {
    cout << a[i] << " ";
  }
}
void arrayInput(vector<float> &a) {
  int n;
  cin >> n;

  if (n < 1) // invalid size!
    return;

  a.resize(n); // create dynamic array with "n" element(s)
  for (int i = 0; i < n; i++) {
    cin >> a[i];
  }
}

int main() {
  vector<float> a;
  cout << "Input an array, first is the number of elements:" << endl;
  arrayInput(a);

  cout << "Element(s) in the array: ";
  arrayOutput(a);
  cout << endl;

  cout << a[a.size() + 1]; //!=> 0

  return 0;
}

```

Trong nhiều trường hợp, nếu ko muốn nhập vào số phần tử thì có thể quy ước kết thúc việc nhập bằng ký tự đặc biệt

```C++
void arrayOutput(vector<float> &a) {
  for (int i = 0; i < a.size(); i++) {
    cout << a[i] << " ";
  }
}
void arrayInput(vector<float> &a) {
  float x;
  while (cin >> x) {
    a.push_back(x);
  }
  // While (cin >> x) khi x ko thỏa kiểu của a, hoặc bất kì kí tự nào gây lỗi cho a.push_back(x) thì việc nhập kết thúc
  // E.g. CtrlX, \, / ,n, trong ví dụ này là mọi dữ liệu nhập vào ko phải là float
}

int main() {
  vector<float> a;
  cout << "Input an array, press Ctrl X then Enter to stop:" << endl;
  arrayInput(a);
  cout << "Element(s) in the array: ";
  arrayOutput(a);
  cout << endl;
  return 0;
}

// Input: 3
// 4
// 2
// true
// Output: Element(s) in the array: 3 4 2
```

### 1.2 Các thao tác trên mảng động

- size(); Trả về số phần tử
- resize(int newsz); Thay đổi kích thước mảng, kích thước mới quy định bởi 'newsz'
- push_back(T x); Thêm phần tử x kiểu T vào cuối mảng, mảng tự động tăng size thêm 1
- pop_back(); Xóa p.tử cuối, size giảm 1

**Ví dụ với vector<int>**

```C++
#include <iostream>
#include <vector>
using namespace std;

// Tạo mảng: tạo một mảng động n phần tử từ một mảng thô
void intArrayMake(vector<int>& a, int elements[], int n) {
  // copy n elements from elements[] to vector<int> a
  int i = 0;
  while (i < n) {
    a.push_back(elements[i]);
    i++;
  }
}

// Xuất mảng: Xuất các p.tử của mảng động ra thiết bị outDev có kiểu ostream, chẳng hạn cout
void intArrayOut(vector<int>& a, ostream& outDev) {
  for (int i = 0; i < a.size(); i++) {
    outDev << a[i] << " ";
  }
  outDev << endl;
}

// Ghép mảng: ghép các phần tử cuối của mảng src vào cuối mảng dest, giữ nguyên những phần tử đang có của mảng dest.
void intArrayCat(vector<int>& dest, vector<int>& src) {
  int size1 = dest.size(), size2 = src.size();
  int idx = size1, newsize = size1 + size2, i = 0;
  dest.resize(newsize);
  while (i < size2) {
    dest[idx] = src[i];
    // Ở đây ko dùng push_back() vì sẽ tăng size, thêm vào dest ở phần tử dest[newsize + 1]
    idx++;
    i++;
  }
}

// Cắt mảng: cắt tất cả các p.tử từ vị trí pos của mảng a, chuyển sang mảng b
void intArrayCutFrom(vector<int>& a, int pos, vector<int>& b){
  int size = a.size(), j = pos;

  if (j < 0 || j > size)
    return;

  b.resize(0);
  while (j < size) {
    b.push_back(a[j]);
    j++;
  }

  a.resize(pos);
}

// Chèn phẩn tử "element" vào vị trí "pos" (position) của vector a chứa kiểu dữ liệu integer
void intArrayInsert(vector<int> & a, int pos, int element) {
  if (pos < 0 || pos > a.size())
    return;
  vector<int> b;
  intArrayCutFrom(a, pos, b); // Cut vector a from position pos, paste to vector b from begin
  a.push_back(element);
  intArrayCat(a, b); // concat vector b from vector a
}

int main() {
  int x[] = {3, 5, 2, 4, 9, 7, 8, 6};
  int n = sizeof(x) / sizeof(int);
  vector<int> a, b, c;

  cout << "\nintArrayMake(a, x, n)\n";
  intArrayMake(a, x, n);
  cout << "a: ";intArrayOut(a, cout);

  cout << "\nintArrayCutFrom(a, 3, b)\n";
  intArrayCutFrom(a, 3, b);
  cout << "now, a: ";intArrayOut(a, cout);
  cout << "b: ";intArrayOut(b, cout);

  cout << "\nintArrayCat(b, a)\n";
  intArrayCat(b, a);
  cout << "now, a: ";intArrayOut(a, cout);
  cout << "now, b: ";intArrayOut(b, cout);

  cout << "\na.pop_back()\n";
  a.pop_back();
  cout << "now, a: ";intArrayOut(a, cout);

  cout << "\nintArrayInsert(b, 3, 999)\n";
  intArrayInsert(b, 3, 999);
  cout << "now, b: ";intArrayOut(b, cout);


  return 0;
}

// OUTPUT:
/*
intArrayMake(a, x, n)
a: 3 5 2 4 9 7 8 6

intArrayCutFrom(a, 3, b)
now, a: 3 5 2
b: 4 9 7 8 6

intArrayCat(b, a)
now, a: 3 5 2
now, b: 4 9 7 8 6 3 5 2

a.pop_back()
now, a: 3 5

intArrayInsert(b, 3, 999)
*/
```

**Tổng quát vector<int> lên thành vector<T>**

```C++
// Tổng quát: cài đặt các hàm cho vector kiểu T

#include <iostream>
#include <vector>
using namespace std;

template<class T>
void intArrayMake(vector<T>& a, T elements[], int n) {
  int i = 0, a.resize(0);
  while (i < n) {
    a.push_back(elements[i]);
    i++;
  }
}

template<class T>
void intArrayOut(vector<T>& a, ostream& outDev) {
  for (int i = 0; i < a.size(); i++) {
    outDev << a[i] << " ";
  }
  outDev << endl;
}

template<class T>
void intArrayCat(vector<T>& dest, vector<T>& src) {
  int size1 = dest.size(), size2 = src.size();
  int idx = size1, newsize = size1 + size2, i = 0;
  dest.resize(newsize);

  while (i < size2) {
    dest[idx] = src[i];
    i++;
    idx++;
  }
}

template<class T>
void intArrayCutFrom(vector<T>& src, int pos, vector<T>& dest) {
  int size = src.size(), j = pos;
  if (j < 0 || j > size)
    return;
  dest.resize(0);

  while (j < size) {
    dest.push_back(src[j]);
    j++;
  }
  src.resize(pos);
}
```

### 1.3 Dùng mảng động nhiều chiều nhờ vector<T>

Dựa vào kiểu vector<T> người lập trình cũng có thể viết chương trình trên thao tác trên mảng động nhiều chiều nhờ cách khai báo lồng nhiều lần dưới dạng mảng của mảng

```C++
// fig 02.07 Mảng nhiều chiều dùng vector<T>
#include <iostream>
#include <vector>
using namespace std;

typedef vector<float> Floats;
typedef vector<Floats> float2D;

void float2DInit(float2D& a, int n){
  a.resize(n);
  for (int i = 0; i < a.size(); i++) {
    a[i].resize(n);
  }
}

void float2D_In(float2D& a) {
  for (int i = 0; i < a.size(); i++)
    for (int j = 0; j < a[i].size(); j++) {
      cin >> a[i][j];
    }
}

void float2D_Out(float2D& a) {
  for (int i = 0; i < a.size(); i++) {
    for (int j = 0; j < a[i].size(); j++) {
      cout << a[i][j] << "\t";
    }
    cout << endl;
  }
}

int main () {
  float2D A;
  int n;
  cout << "Size of square float array: ";
  cin >> n;
  if (n <= 0) {
    cout << "Invalid size..." << endl;
    return 0;
  }

  float2DInit(A, n);
  cout << "Input elements of square array: " << endl;
  float2D_In(A);

  cout << "Elements of square array: " << endl;
  float2D_Out(A);
  return 0;
}

/*
Input: 3 3 2 4 5 67 8 4.6 4 5
Output:
Size of square float array:  3 3 2 4 5 67 8 4.6 4 5
Input elements of square array:
! Elements of square array:
3       2       4
5       67      8
4.6     4       5
*/
```

## II. Biến con trỏ

### 2.1 Khái niệm về biến con trỏ

Biến con trỏ dùng để lưu địa chỉ của những biến khác hay địa chỉ một vùng nhớ hợp lệ.
Thông thường, vùng nhớ được cấp phát nhờ gọi các hàm có sẳn trong thư việc để xin bộ nhớ từ hệ điều hành.
Biến chưa khởi tạo sẽ nhận giá trị rác.
Con trỏ chưa gán địa chỉ sẽ không sử dụng được.

```C++
// fig 02.07 Mảng nhiều chiều dùng vector<T>
#include <iostream>
using namespace std;

int main () {
  int* p;
  int a = 19, b;
  // Hai biến p và p chưa khởi gán
  // tại đây, gán *p = 9 sẽ lỗi vì biến p chưa giữ địa chỉ hợp lệ
  cout << "\na = " << a << "; &a = " << &a; //=> 23
  cout << "\nb = " << b << "; &b = " << &b; //! => giá trị rác là số nguyên
  // cout << "\n*p = " << *p; //!=> error: segmentation fault. gây lỗi do vùng nhớ p chưa có địa chỉ hợp lệ
  cout << "\np = " << p; //! => giá trị rác bộ nhớ, không hợp lệ khi sử dụng
  cout << "\n\nTest: p = &a; *p = 23;";
  p = &a;
  *p = 23;
  cout << "\np = " << p; //=> giá trị rác do chưa khai báo
  cout << "\na = " << a << "; &a = " << &a; //=> 23
  cout << "\nb = " << b << "; &b = " << &b; //=> giá trị rác do chưa khai báo
  cout << "\n*p = " << *p; //=> giá trị rác do chưa khai báo


  cout << "\n\nTest: p = &b; *p = 31;";
  p = &b;
  *p = 31;
  cout << "\np = " << p; //=> giá trị rác do chưa khai báo
  cout << "\na = " << a << "; &a = " << &a; //=> 23
  cout << "\nb = " << b << "; &b = " << &b; //=> giá trị rác do chưa khai báo
  cout << "\n*p = " << *p; //=> giá trị rác do chưa khai báo
  cout << endl;
  return 0;
}
```

### 2.2 Việc gán biến con trỏ hay cấp phát bộ nhớ

Hầu hết các hệ điều hành ngăn ngừa việc truy cập trực tiếp vào địa chỉ bộ nhớ vật lý hay các tài nguyên phần cứng.
Vì vậy, địa chỉ bộ nhớ khi gán vào biến con trỏ phả được lấy từ địa chỉ của một biến, một vùng nhớ có sẵn hay là trả về nhờ gọi hàm để truy cập vào các tài nguyên mà hệ điều hành cho phép, thông thường nhất là hàm cấp phát bộ nhớ.

#### 2.2.1. Gán con trỏ bằng địa chỉ của biến

**Ví dụ về gán biến con trỏ**

Biến con trỏ có thể được gán bằng với địa chỉ của một biến hay một phần tử của mảng.

Trường hợp khác kiểu thì phải có thao tác ép kiểu một cách phù hợp. Đặc biệt, biến con trỏ vô kiểu (void\* p) có thể nhận địa chỉ của bất kỳ biến nào.

```C++
// fig 02.09
#include <stdio.h>
int main() {
  float x;
  unsigned short a[10] = {0};
  // Mảng a[] khai báo gồm 10 phần tử có kiểu nền là unsigned short (độ dài 2-byte) nên là một dãy 10x2 = 20 byte liên tục trong bộ nhớ máy tính, khởi đầu là a[0]. Vì vậy địa chỉ đầu a[0] cũng là địa chỉ đầu của mảng a[]. Trong C/C++ ký hiệu địa chỉ của a[0] là &a[0], còn ký hiệu địa chỉ đầu của mảng a[] là a; tức là &a[0] và a là như nhau (cùng địa chỉ bộ nhớ).
  unsigned short* pshort;
  unsigned long* plong;
  void *p;

  pshort = a;               // tương đương với p = &a[0]
  *pshort = 5;              // tương đương với a[0] = 5
  pshort = &a[2];
  *pshort = 19;             // tương đương với a[2] = 19

  p = &x;
  *(float *)p = 1.5F;       // tương đương với x = 1.5F
                            //! Ép kiểu con trỏ void về kiểu con trỏ float
  p = &a[8];
  *(unsigned short *)p = 23;// a[8] = 23
  //! Ép kiểu con trỏ float về kiểu con trỏ unsigned short

  plong = (unsigned long *)&a[5];
  //! Ép kiểu địa chỉ kiểu unsigned short về kiểu địa chỉ kiểu unsigned long
  *plong = 0xDEADBEEF;      // tác động lên cả a[5] lẫn a[6]
  //...
  return 0;
}
```

#### 2.2.2. Con trỏ là tham số hay trị trả về của hàm

#### 2.2.3. Gán con trỏ bằng địa chỉ bộ nhớ xin cấp phát

### 2.3. Kỹ thuật thao tác và tính địa chỉ dựa vào con trỏ

#### 2.3.1 Tính toán số học trên địa chỉ bộ nhớ

#### 2.3.2 Địa chỉ bộ nhớ vật lý và địa chỉ logic

#### 2.3.3 Một vài kỹ thuật áp dụng con trỏ

#### 2.3.4. Một số nguyên tắc an toàn khi dùng con trỏ

## 3. Cấp phát động và sử dụng dữ liệu động

### 3.1 Vùng nhớ heap và vùng nhớ stack

### 3.2 Các hàm khai thác bộ nhớ động

#### 3.2.1 Các hàm cấp phát và giải phóng bộ nhớ

#### 3.2.2 Các hàm hỗ trợ về bộ nhớ động

#### 3.2.3 Các hàm thao tác trên bộ nhớ

### 3.3 Cấp phát và dùng mảng một chiều

#### 3.3.1 Xác định số phần tử trước khi cấp phát

#### 3.3.2 Cấp phát nới rộng dần

#### 3.3.3 Việc bổ sung các hàm thư viện mảng một chiều

#### 3.3.4 Toán tử chỉ số mảng trong cấu trúc

### 3.4 Kỹ thuật kiêm tra con trỏ an toàn

### 3.5 Mảng động hai chiều và nhiều chiều

#### 3.5.1 Việc tổ chức bộ nhớ cho mảng động nhiều chiều

#### 3.5.2 Mảng hai chiều với số dòng cố định

#### 3.5.3 Mảng hai chiều với số cột cố dịnh

#### 3.5.4 Mảng hai chiều có dòng và cột biến động

#### 3.5.5 Bổ sung hàm thư viện về mảng động hai chiều

#### 3.5.6 Mảng động nhiều chiều

### 3.6 Tầm quan trọng của giải phóng bộ nhớ

## 4. Dữ liệu có cấu trúc và con trỏ

### 4.1 Đặc điểm kỹ thuật của biến cấu trúc

### 4.2 Kỹ thuật cấu trúc hóa vùng bộ nhớ

### 4.3 Toán tử chỉ số của biến cấu trúc

#### 4.3.1 Một số ví dụ minh họa về mảng vòng

#### 4.3.2 Cài đặt mảng động nhiều chiều nhớ toán tử chỉ số

### 4.4 Mảng động gồm các biến cấu trúc

## 5. Các vấn đề củng cố và mở rộng kiến thức

### 5.1 Biến cấu trúc tự khởi động và tự hủy

### 5.2 Con trỏ vô kiểu

### 5.3 Biến tham chiếu

### 5.4 Con trỏ hằng, mảng tĩnh và kiến thức liên quan

### 5.5 Con trỏ hàm

## 6. Thubật ngữ tiếng anh
