src
└── main
    ├── java
    │   └── com.example.webappM
    │       ├── WebAppApplication.java  // File chính để khởi động ứng dụng
    │       ├── controller              // Package chứa các lớp controller
    │       ├── model                   // Package chứa các lớp model (entity)
    │       ├── repository              // Package chứa các lớp repository
    │       └── service                 // Package chứa các lớp service
    └── resources
        ├── static                      // Chứa tài nguyên tĩnh (CSS, JS, hình ảnh)
        ├── templates                   // Chứa các file HTML sử dụng Thymeleaf
        └── application.properties      // Cấu hình của ứng dụng
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.h2.console.enabled=true
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://localhost:3306/webapp
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
        package com.example.webapp.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String description;
    private double price;

    // Getters and Setters
}
package com.example.webapp.repository;

import com.example.webapp.model.Product;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
}
package com.example.webapp.service;

import com.example.webapp.model.Product;
import com.example.webapp.repository.ProductRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }

    public void addProduct(Product product) {
        productRepository.save(product);
    }

    public Product getProductById(Long id) {
        return productRepository.findById(id).orElse(null);
    }

    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }
}
package com.example.webapp.controller;

import com.example.webapp.model.Product;
import com.example.webapp.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/products")
    public String listProducts(Model model) {
        model.addAttribute("products", productService.getAllProducts());
        return "product-list";
    }

    @GetMapping("/products/add")
    public String addProductForm(Model model) {
        model.addAttribute("product", new Product());
        return "product-form";
    }

    @PostMapping("/products")
    public String addProduct(@ModelAttribute Product product) {
        productService.addProduct(product);
        return "redirect:/products";
    }
}
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<title>Product List</title>
</head>
<body>
<h1>Product List</h1>
    <ul>
        <li th:each="product : ${products}">
            <span th:text="${product.name}">Product Name</span> -
            <span th:text="${product.price}">Product Price</span>
        </li>
    </ul>
    <a href="/products/add">Add Product</a>
</body>
</html>
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<title>Add Product</title>
</head>
<body>
<h1>Add Product</h1>
    <form action="#" th:action="@{/products}" th:object="${product}" method="post">
        <input type="text" th:field="*{name}" placeholder="Product Name" required/>
        <textarea th:field="*
