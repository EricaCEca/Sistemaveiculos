# Sistemaveiculos
Nesse projeto temos como principais elementos  as entidades em nosso sistema contendo os Veículos: Atributos: id, modelo, marca, ano, cor, preço, quilometragem, status. 
Nesse projeto temos como principais elementos  as entidades em nosso sistema contendo os Veículos: Atributos: id, modelo, marca, ano, cor, preço, quilometragem, status. Métodos: cadastrar(), atualizar(), remover(), consultar(). Em relação a Marca temos o atributos: Id Nome, Métodos cadastrarMarca(), ListarMarcas() e em relação ao Modelo: atributos id Nome, Marca com Métodos: cadastrarModelo(), listarModeloPorMarca().

vehicle-management/
│
├─ src/main/java/com/example/vehicle/
│   ├─ VehicleManagementApplication.java
│   ├─ model/
│   │   ├─ Vehicle.java
│   │   ├─ Brand.java
│   │   └─ Model.java
│   ├─ repository/
│   │   ├─ VehicleRepository.java
│   │   ├─ BrandRepository.java
│   │   └─ ModelRepository.java
│   ├─ service/
│   │   ├─ VehicleService.java
│   │   └─ BrandService.java


│   └─ controller/
│       ├─ VehicleController.java
│       └─ BrandController.java
│
├─ src/main/resources/
│   └─ application.properties



<dependencies>
    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Boot Starter Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Banco de dados H2 (para teste) -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- Lombok para gerar getters/setters automaticamente -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
package com.example.vehicle.model;

import jakarta.persistence.*;
import lombok.Data;

@Data
@Entity
public class Vehicle {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String model;
    private String brand;
    private int year;
    private String color;
    private double price;
    private int mileage;
    private String status; // Disponível, Vendido

}
package com.example.vehicle.model;

import jakarta.persistence.*;
import lombok.Data;

@Data
@Entity
public class Brand {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
}
package com.example.vehicle.model;

import jakarta.persistence.*;
import lombok.Data;

@Data
@Entity
public class Model {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    private Brand brand;
}
package com.example.vehicle.repository;

import com.example.vehicle.model.Vehicle;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface VehicleRepository extends JpaRepository<Vehicle, Long> {
    List<Vehicle> findByBrand(String brand);
    List<Vehicle> findByYear(int year);
}
package com.example.vehicle.repository;

import com.example.vehicle.model.Brand;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface BrandRepository extends JpaRepository<Brand, Long> { }

│
└─ pom.xml
package com.example.vehicle.repository;

import com.example.vehicle.model.Model;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ModelRepository extends JpaRepository<Model, Long> { }
package com.example.vehicle.service;

import com.example.vehicle.model.Vehicle;
import com.example.vehicle.repository.VehicleRepository;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class VehicleService {

    private final VehicleRepository repository;

    public VehicleService(VehicleRepository repository) {
        this.repository = repository;
    }

    public Vehicle createVehicle(Vehicle vehicle) {
        return repository.save(vehicle);
    }

    public List<Vehicle> getAllVehicles() {
        return repository.findAll();
    }

    public Optional<Vehicle> getVehicleById(Long id) {
        return repository.findById(id);
    }

    public Vehicle updateVehicle(Long id, Vehicle vehicle) {
        vehicle.setId(id);
        return repository.save(vehicle);
    }

    public void deleteVehicle(Long id) {
        repository.deleteById(id);
    }

    public List<Vehicle> findByBrand(String brand) {
        return repository.findByBrand(brand);
    }

    public List<Vehicle> findByYear(int year) {
        return repository.findByYear(year);
    }
}
package com.example.vehicle.controller;

import com.example.vehicle.model.Vehicle;
import com.example.vehicle.service.VehicleService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/vehicles")
@CrossOrigin
public class VehicleController {

    private final VehicleService service;

    public VehicleController(VehicleService service) {
        this.service = service;
    }

    @PostMapping
    public Vehicle createVehicle(@RequestBody Vehicle vehicle) {
        return service.createVehicle(vehicle);
    }

    @GetMapping
    public List<Vehicle> getAllVehicles() {
        return service.getAllVehicles();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Vehicle> getVehicleById(@PathVariable Long id) {
        return service.getVehicleById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PutMapping("/{id}")
    public Vehicle updateVehicle(@PathVariable Long id, @RequestBody Vehicle vehicle) {
        return service.updateVehicle(id, vehicle);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteVehicle(@PathVariable Long id) {
        service.deleteVehicle(id);
        return ResponseEntity.noContent().build();
    }

    @GetMapping("/brand/{brand}")
    public List<Vehicle> getVehiclesByBrand(@PathVariable String brand) {
        return service.findByBrand(brand);
    }

    @GetMapping("/year/{year}")
    public List<Vehicle> getVehiclesByYear(@PathVariable int year) {
        return service.findByYear(year);
    }
}
package com.example.vehicle;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class VehicleManagementApplication {

    public static void main(String[] args) {
        SpringApplication.run(VehicleManagementApplication.class, args);
    }
}
