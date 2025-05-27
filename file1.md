### **Estructura Correcta de la Base de Datos con Laravel y MongoDB**

Para cumplir con los criterios del proyecto, la base de datos debe diseñarse usando **MongoDB** y relaciones mediante **Eloquent ORM**. A continuación, se detalla la estructura de modelos, relaciones y colecciones:

---

#### **1. Modelos Principales y sus Campos**

| **Modelo**       | **Colección**    | **Campos (Ejemplos)**                                                                                     |
|-------------------|------------------|------------------------------------------------------------------------------------------------------------|
| **Freelancer**   | `freelancers`    | `name`, `portfolio` (array de objetos), `experience` (array), `education` (array), `certifications` (array), `languages` (array), `availability`, `rates` (array). |
| **Client**       | `clients`        | `name`, `email`, `company`, `contact_info`.                                                               |
| **Project**      | `projects`       | `client_id`, `title`, `description`, `budget`, `timeline`, `deliverables` (array), `evaluation_criteria`. |
| **Proposal**     | `proposals`      | `freelancer_id`, `project_id`, `approach`, `timeline_details`, `budget_breakdown`, `examples`, `terms`, `status` (`pending`, `accepted`, `rejected`). |
| **Milestone**    | `milestones`     | `proposal_id`, `title`, `due_date`, `deliverables` (array), `feedback` (array), `adjustments` (array), `status` (`in_progress`, `completed`). |
| **Payment**      | `payments`       | `milestone_id`, `amount`, `status` (`held`, `released`, `disputed`), `platform_commission`, `dispute_details`. |
| **Review**       | `reviews`        | `project_id`, `reviewer_id` (polimórfico), `reviewer_type` (`Client` o `Freelancer`), `reviewed_id` (polimórfico), `reviewed_type`, `rating`, `comments`. |
| **Skill**        | `skills`         | `name`, `category` (ej: `software_development`, `graphic_design`).                                        |

---

#### **2. Relaciones entre Modelos (Eloquent ORM)**

| **Modelo**       | **Relaciones**                                                                                                                                                  |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Freelancer**   | - `hasMany(Proposal::class)` <br> - `belongsToMany(Skill::class, 'freelancer_skill')` (usando la colección pivote `freelancer_skill`).                          |
| **Client**       | - `hasMany(Project::class)`.                                                                                                                                    |
| **Project**      | - `belongsTo(Client::class)` <br> - `hasMany(Proposal::class)` <br> - `belongsToMany(Skill::class, 'project_skill')` (colección pivote `project_skill`).         |
| **Proposal**     | - `belongsTo(Freelancer::class)` <br> - `belongsTo(Project::class)` <br> - `hasMany(Milestone::class)`.                                                         |
| **Milestone**    | - `belongsTo(Proposal::class)` <br> - `hasOne(Payment::class)`.                                                                                                 |
| **Payment**      | - `belongsTo(Milestone::class)`.                                                                                                                                |
| **Review**       | - `morphTo('reviewer')` (para `Client` o `Freelancer`) <br> - `morphTo('reviewed')` (para `Client` o `Freelancer`) <br> - `belongsTo(Project::class)`.          |
| **Skill**        | - `belongsToMany(Freelancer::class, 'freelancer_skill')` <br> - `belongsToMany(Project::class, 'project_skill')`.                                               |

---

#### **3. Estructura de Migraciones**

Ejemplo de migraciones necesarias (ejecutar con `php artisan make:migration`):

```php
// Crear colección 'freelancers'
Schema::create('freelancers', function (Blueprint $collection) {
    $collection->index('name'); // Índice para búsquedas rápidas
});

// Crear colección pivote 'freelancer_skill'
Schema::create('freelancer_skill', function (Blueprint $collection) {
    $collection->index('freelancer_id');
    $collection->index('skill_id');
});

// Crear colección 'projects'
Schema::create('projects', function (Blueprint $collection) {
    $collection->index('client_id'); // Índice para relación con Client
});
```

---

#### **4. Configuración de Eager Loading**

Para optimizar consultas, cargue relaciones anidadas usando `with()`:

```php
// Ejemplo: Obtener un proyecto con propuestas, freelancers y habilidades
$project = Project::with(['proposals.freelancer', 'skills'])->find($id);
```

---

#### **5. Ejemplo de Modelo Freelancer**

```php
// app/Models/Freelancer.php
class Freelancer extends Model
{
    protected $collection = 'freelancers';

    // Relación con propuestas
    public function proposals() {
        return $this->hasMany(Proposal::class);
    }

    // Relación many-to-many con habilidades
    public function skills() {
        return $this->belongsToMany(Skill::class, 'freelancer_skill');
    }
}
```

---

#### **6. Ejemplo de Relación Polimórfica en Review**

```php
// app/Models/Review.php
class Review extends Model
{
    public function reviewer() {
        return $this->morphTo();
    }

    public function reviewed() {
        return $this->morphTo();
    }
}

// Uso en controlador:
$review = Review::with(['reviewer', 'reviewed'])->find($id);
```

---

#### **7. Validaciones y API Resources**

- **Form Requests**: Cree clases como `StoreProjectRequest` para validar datos entrantes (ej: `budget` debe ser numérico).
- **Resources**: Transforme modelos a JSON estructurado (ej: `FreelancerResource` incluye `skills` y `proposals`).

---

#### **8. Paginación**

En endpoints de listado, use `paginate()`:

```php
// Ejemplo en FreelancerController
public function index() {
    return FreelancerResource::collection(Freelancer::paginate(10));
}
```

---

### **Diagrama de la Base de Datos**

```
freelancers
├── _id
├── name
├── skills (array de skill_ids)
└── proposals (relación hasMany)

projects
├── _id
├── client_id
├── skills (array de skill_ids)
└── proposals (relación hasMany)

proposals
├── _id
├── freelancer_id
├── project_id
└── milestones (relación hasMany)

reviews
├── reviewer_id (polimórfico)
├── reviewer_type (Client/Freelancer)
├── reviewed_id (polimórfico)
└── reviewed_type (Client/Freelancer)
```

---

### **Notas Clave**

1. **MongoDB + Eloquent**: Use el paquete `jenssegers/laravel-mongodb` para integrar MongoDB con Laravel.
2. **Relaciones Polimórficas**: Esenciales para el sistema bidireccional de reseñas.
3. **Eager Loading**: Asegúrese de cargar relaciones anidadas para evitar el problema N+1.
4. **Índices**: Defina índices en migraciones para campos usados en búsquedas (ej: `client_id` en `projects`).

Esta estructura cumple con los criterios técnicos y permite escalar la plataforma eficientemente.
