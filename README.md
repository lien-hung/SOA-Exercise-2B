# Mô tả
Bài tập này sử dụng framework ASP.NET để tạo một API đơn giản thực hiện các thao tác CRUD trên cơ sở dữ liệu MongoDB về cửa hàng sách (`BookStore`): thêm/sửa/xóa, truy xuất thông tin về sách (`Book`).

# Triển khai chương trình
## Dữ liệu ban đầu
Database `BookStore` có một collection `Book`, trong đó gồm 2 document với dữ liệu như bên dưới:

```
{
  "_id": {
    "$oid": "678cb0da5c81dae26a7da41a"
  },
  "Name": "Design Patterns",
  "Price": 54.93,
  "Category": "Computers",
  "Author": "Ralph Johnson"
}

{
  "_id": {
    "$oid": "678cb0da5c81dae26a7da41b"
  },
  "Name": "Clean Code",
  "Price": 43.15,
  "Category": "Computers",
  "Author": "Robert C. Martin"
}
```

## Thiết kế và bổ sung
Phần code được thực hiện như trong hướng dẫn nhưng có bổ sung thêm validation để tránh cho dữ liệu bị sai sót. Validation đặt trong controller ở phương thức thêm và sửa document để đảm bảo một đầu sách được thêm hoặc sửa (`newBook`) phải có giá bán lớn hơn 0 và không trùng tiêu đề với bất kỳ đầu sách nào khác đang có trong cơ sở dữ liệu.

```C#
if (currentBooks.Exists(book => book.BookName == newBook.BookName.Trim())
    || newBook.Price <= 0)
{
    return BadRequest();
}
```

## Kết quả đạt được
* **Lấy danh sách `Book` từ database** (GET)

  ![image](https://github.com/user-attachments/assets/54b63a21-42e8-49c1-a894-dd5664552d95)
  
* **Thêm một `Book` vào database** (POST)
  * Dữ liệu hợp lệ
    ```
    {
      "Id": "678cb0da5c81dae26a7da41c",
      "Name": "Service Architecture",
      "Price": 38.77,
      "Category": "Computers",
      "Author": "Vo Van Hai"
    }
    ```
    ![image](https://github.com/user-attachments/assets/f408485f-131d-4f76-a701-982d58595cb9)
  * Sách có giá dưới 0
    ```
    {
      "Id": "678cb0da5c81dae26a7da41d",
      "Name": "Algorithms",
      "Price": -55.16,
      "Category": "Computers",
      "Author": "Donald Knuth"
    }
    ```
    ![image](https://github.com/user-attachments/assets/f4aa2a1a-c506-4475-8b12-42a3e1ded895)
* **Lấy một `Book` theo ID** (GET)

  ![image](https://github.com/user-attachments/assets/a1a9afa8-d7c4-4e56-a29c-e92c253debd4)
  ![image](https://github.com/user-attachments/assets/c341bf39-5609-4c4d-90bc-80ee91b3d62c)
  
* **Sửa thông tin một `Book`** (PUT)
  * Dữ liệu hợp lệ
    ```
    {
      "Id": "678cb0da5c81dae26a7da41c",
      "Name": "Web Services",
      "Price": 50.24,
      "Category": "Computers",
      "Author": "Vo Van Hai"
    }
    ```
    ![image](https://github.com/user-attachments/assets/7bdee74b-8576-4939-b905-bbb2bdad8604)
    ![image](https://github.com/user-attachments/assets/66700425-8342-4e70-adea-4855c3ad4036)
  * Sách trùng tên
    ```
    {
      "Id": "678cb0da5c81dae26a7da41c",
      "Name": "Design Patterns",
      "Price": 50.24,
      "Category": "Computers",
      "Author": "Vo Van Hai"
    }
    ```
    ![image](https://github.com/user-attachments/assets/6697e49a-2a85-4a42-a5f1-b8c65dae08ca)
* **Xóa một `Book`** (DELETE)

  ![image](https://github.com/user-attachments/assets/5ebaf7f3-4232-4eac-9c32-6e311cec0ccf)
  
  Sau khi xóa, nếu gửi lại request PUT theo ID sách đã xóa thì response có mã 404 (Not Found).
  
  ![image](https://github.com/user-attachments/assets/5356ec36-8a47-4748-82a6-0e51821fed16)

# Ghi chú
Mã nguồn được tham khảo theo [hướng dẫn](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-9.0&tabs=visual-studio) của Microsoft.
