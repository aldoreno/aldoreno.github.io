---
title: "Penggunaan 'const' di C++"
date: "2013-02-15"
categories: 
  - "code"
tags: 
  - "c"
  - "const"
  - "dynamic-allocation"
  - "function-overloading"
  - "pointer"
  - "reference"
  - "stack-versus-heap"
---

Penggunaan konstanta di C++ ternyata unik sekali. Selain membuat konstanta, const juga bisa digunakan untuk hal lain.

Perhatikan CRectangle.h baris 23. Penggunaan const di akhir definisi fungsi membuatnya bersifat _read-only_. _Compiler_ akan mengeluarkan _error_ jika terdapat _statement_ di dalam fungsi tersebut yang berusaha untuk mengubah _state_ milik _instance_.

```c++
class CRectangle {

  // private member declaration
  private:
    int *width, *height;
  
  // public member declaration
  // function definition is in its .cpp
  public:
    // constructor declaration
    CRectangle(int, int);
  
    // destructor declaration
    ~CRectangle();
  
    int area() {
      modifyThis = 0;
      return ((*width) * (*height));
    }
  
    int constArea() const {
      /* error. read-only memory: 
       modifyThis = 0;
       */
      
      return ((*width) * (*height));
    }
  
    int modifyThis;
}; 

#include "CRectangle.h"

// public member function definition
// constructor definition
CRectangle::CRectangle(int a, int b) {
  width = new int;
  height = new int;
    
  *width = a;
  *height = b;
}

// destructor definition
CRectangle::~CRectangle(){
  delete width;
  delete height;
}

#include "CRectangle.h"

void test(const CRectangle &);
void test(CRectangle &);
void test(CRectangle *);

int main(int argc, const char * argv[]) {
  // create new CRectangle instance
  // that is stored in stack
  CRectangle rect(3, 4);
  
  // create new CRectangle instance
  // that's stored in heap that's why
  // we need a pointer to keep a reference
  // of it
  CRectangle *pRect = new CRectangle(1,100);
  
  
  // void test(CRectangle &aliasRect)
  test(*pRect);
  
  // void test(CRectangle *aliasRect)
  test(pRect);
  
  // void test(const CRectangle &aliasRect)
  // can be called this way:
  const CRectangle &aRect = *pRect;
  test(aRect);
  
  // or, we can cast existing object
  // pointed by a pointer to conform
  // test() function signature:
  test((const CRectangle &) *pRect);
  
  
  // object made in heap needs
  // to be manually deallocated
  delete pRect;
  
  return 0;
}

void test(const CRectangle &aliasRect) {
  std::cout << "void test(const CRectangle &aliasRect);" << std::endl;
  std::cout << aliasRect.constArea() << std::endl;
  
  // below line will produce compile error
  // because the function we're about to
  // call is not declared itself to be
  // read-only:
  // std::cout << aliasRect.area() << std::endl;
  
  // below line will also produce compile error
  // because we can not modify aliasRect
  // state (it members). we can only read them:
  // aliasRect.modifyThis = 100;
}

void test(CRectangle &aliasRect) {
  std::cout << "void test(CRectangle &aliasRect);" << std::endl;
  std::cout << aliasRect.area() << std::endl;
  std::cout << aliasRect.constArea() << std::endl;
}

void test(CRectangle *aliasRect) {
  std::cout << "void test(CRectangle *aliasRect);" << std::endl;
  std::cout << aliasRect->area() << std::endl;
  std::cout << aliasRect->constArea() << std::endl;
} 
```

Pada baris 45 berkas main.cpp, parameter CRectangle &aliasRect mengindikasikan bahwa aliasRect adalah alias (atau lebih tepatnya referensi) terhadap object CRectangle sesungguhnya.

Hal ini disebut _pass-by-reference_. Salah satu tujuannya adalah optimasi: menghindari dibuatnya kopian (salinan) dari parameter yang dikirim. Bayangkan jika parameter yang dikirim adalah object berukuran besar. Pendekatan _pass-by-reference_ akan meniadakan penggunaan waktu dan ruang yang dibutuhkan untuk membuat kopian dari object tersebut.

Tidak berhenti disitu. Masih di baris yang sama kita bisa lihat penggunaan const sebelum tipe parameter. Ini mengindikasikan bahwa rutin di dalam fungsi tersebut tidak boleh mengubah aliasRect. Lebih jauh lagi: _compiler_ yang akan memastikan bahwa hanya _methods_ milik object aliasRect yang sifatnya _read-only_ yang boleh dipanggil.

Jika ingin tahu lebih detail lagi tentang konstanta di C++, baca ini: [The C++ 'const' Declaration: Why & How](http://duramecho.com/ComputerInformation/WhyHowCppConst.html "The C++ 'const' Declaration: Why & How")

Contoh code yang kusertakan di atas mencakup beberapa topik lain, diantaranya: penggunaan pointer di C++, stack versus heap (dynamic allocation), function overloading.
