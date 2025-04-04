# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [X] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [X] Commit: `Create Subscriber model struct.`
    -   [X] Commit: `Create Notification model struct.`
    -   [X] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [X] Commit: `Implement add function in Subscriber repository.`
    -   [X] Commit: `Implement list_all function in Subscriber repository.`
    -   [X] Commit: `Implement delete function in Subscriber repository.`
    -   [X] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [X] Commit: `Create Notification service struct skeleton.`
    -   [X] Commit: `Implement subscribe function in Notification service.`
    -   [X] Commit: `Implement subscribe function in Notification controller.`
    -   [X] Commit: `Implement unsubscribe function in Notification service.`
    -   [X] Commit: `Implement unsubscribe function in Notification controller.`
    -   [X] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [X] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [X] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [X] Commit: `Implement publish function in Program service and Program controller.`
    -   [X] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [X] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Menurut saya, dalam kasus ini sudah cukup menggunakan satu Model struct saja, yaitu `Subscriber`, tanpa perlu menggunakan interface/trait. Hal ini dikarenakan hanya ada satu observer. Penggunaan interface akan lebih berguna ketika kita memiliki banyak observer dengan tipe yang berbeda.

2. Penggunaan `DashMap` saat ini sudah tepat, dibandingkan menggunakan `Vec`. Hal ini karena `DashMap` menjamin keunikan `id` dalam `Product` dan `url` dalam `Subscriber` secara otomatis, memungkinkan pencarian cepat dengan kompleksitas O(1) dibandingkan O(n) pada `Vec`, serta memudahkan penghapusan tanpa perlu _reallocation_. Selain itu, `DashMap` mendukung akses _concurrent_, sehingga lebih aman digunakan jika aplikasi tersebut digunakan dalam lingkungan _multi-thread_.

3. Karena BambangShop berjalan secara _multi-threading_, maka penggunaan `DashMap` akan lebih tepat daripada menggunakan Singleton pattern. `DashMap` menyediakan _thread-safe_ bawaan dengan mekanisme _locking_ yang lebih efisien. Hal ini memungkinkan data `SUBSCRIBERS` diakses secara bersamaan tanpa masalah. Jika kita menggunakan pendekatan Singleton pattern, objek hanya memiliki satu instance selama program dijalankan dan tidak menawarkan efisiensi dalam akses data yang bersamaan.

#### Reflection Publisher-2
1. Memisahkan Service dan Repository dari Model dalam arsitektur perangkat lunak memberikan berbagai manfaat berdasarkan prinsip desain seperti Single Responsibility Principle (SRP). Meskipun Model dalam MVC mencakup logika bisnis dan penyimpanan data, mencampurnya dalam satu tempat dapat menyulitkan dalam _maintainability_ seiring pertumbuhan aplikasi. Repository berfungsi sebagai lapisan yang menangani interaksi langsung dengan sumber data seperti database, cache, atau API eksternal, memungkinkan perubahan pada penyimpanan data tanpa memengaruhi logika bisnis. Sementara itu, Service bertugas mengelola aturan bisnis dan koordinasi antar model atau repositori, menjadikan kode lebih modular dan mudah diuji. Pemisahan ini menghasilkan kode yang lebih bersih, fleksibel, serta lebih mudah dikembangkan seiring meningkatnya kompleksitas sistem.

2. Jika hanya menggunakan Model tanpa memisahkan Service dan Repository, kompleksitas kode akan meningkat secara signifikan karena Model harus menangani berbagai tanggung jawab sekaligus, yaitu menangani struktur data, logika bisnis, dan juga interaksi dengan penyimpanan data. Selain itu, _coupling_ antar class juga menjadi lebih tinggi, yang artinya jika terjadi perubahan pada suatu bagian dapat menyebabkan banyak perubahan lain.

3. Postman sangat membantu dalam _testing_ API yang saya kembangkan. Postman memungkinkan saya untuk mengirim request HTTP, seperti `GET`, `POST`, `PUT`, dan `DELETE` dengan mudah, melihat respons dari server, serta menangani autentikasi dan _header request_ tanpa harus menulis kode tambahan. Selain itu, terdapat fitur Collection & Environment yang memungkinkan saya menyimpan dan mengelola berbagai _request_ API untuk proyek yang dikerjakan.

#### Reflection Publisher-3
1. Pada tutorial ini, Observer Pattern yang digunakan adalah Push Model karena Publisher mengirimkan notifikasi ke semua Subscriber-nya setiap kali terjadi perubahan (_create_, _delete_, _update_) pada Product, menggunakan metode `notify` dalam `NotificationService`.

2. Jika menggunakan Pull Model, keuntungannya adalah Subscriber dapat menentukan notifikasi apa yang ingin mereka terima dari Publisher, sehingga lebih fleksibel untuk Subscriber. Namun, kekurangannya adalah Subscriber harus secara aktif meminta data (`pull`) ke Publisher secara berkala, yang dapat menyebabkan pulling berulang dan meningkatkan beban sistem jika dilakukan terlalu sering.

3. Tanpa _multi-threading_, proses notifikasi akan berjalan secara _sequential_ dan memakan waktu yang lama karena _bottleneck_ terjadi pada saat Publisher mengirimkan notifikasi ke semua Subscriber di class `NotificationService` melalui metode `notify`. Hal ini dapat menyebabkan seluruh sistem menjadi lambat dan tidak responsif, karena operasi lain harus menunggu hingga proses notifikasi selesai.