```json
{
  "freelancers": [
    {
      "_id": "507f1f77bcf86cd799439011",
      "name": "John Doe",
      "portfolio": [
        {
          "title": "Web App",
          "description": "Built with Laravel",
          "url": "https://example.com"
        }
      ],
      "experience": [
        {
          "position": "Senior Developer",
          "company": "Tech Corp",
          "years": 5
        }
      ],
      "education": [
        {
          "degree": "BSc Computer Science",
          "university": "Tech University"
        }
      ],
      "certifications": ["AWS Certified"],
      "languages": ["English", "Spanish"],
      "availability": "full-time",
      "rates": [
        {
          "type": "hourly",
          "amount": 50
        }
      ],
      "skills": ["607f1f77bcf86cd799439031"],
      "proposals": ["607f1f77bcf86cd799439021"]
    }
  ],

  "clients": [
    {
      "_id": "507f1f77bcf86cd799439012",
      "name": "Alice Smith",
      "email": "alice@example.com",
      "company": "Innovate LLC",
      "contact_info": {
        "phone": "+1234567890",
        "address": "123 Main St"
      },
      "projects": ["507f1f77bcf86cd799439013"]
    }
  ],

  "projects": [
    {
      "_id": "507f1f77bcf86cd799439013",
      "client_id": "507f1f77bcf86cd799439012",
      "title": "E-commerce Platform",
      "description": "Build a custom e-commerce solution",
      "budget": 10000,
      "timeline": "6 months",
      "deliverables": ["API", "Frontend"],
      "evaluation_criteria": ["Quality", "Timeliness"],
      "skills": ["607f1f77bcf86cd799439031"],
      "proposals": ["607f1f77bcf86cd799439021"]
    }
  ],

  "proposals": [
    {
      "_id": "607f1f77bcf86cd799439021",
      "freelancer_id": "507f1f77bcf86cd799439011",
      "project_id": "507f1f77bcf86cd799439013",
      "approach": "Agile development",
      "timeline_details": "3 months",
      "budget_breakdown": {
        "development": 8000,
        "design": 2000
      },
      "status": "pending",
      "milestones": ["707f1f77bcf86cd799439041"]
    }
  ],

  "milestones": [
    {
      "_id": "707f1f77bcf86cd799439041",
      "proposal_id": "607f1f77bcf86cd799439021",
      "title": "Design Phase",
      "due_date": "2023-12-01T00:00:00Z",
      "deliverables": ["UI/UX Mockups"],
      "status": "in_progress",
      "payment_id": "807f1f77bcf86cd799439051"
    }
  ],

  "payments": [
    {
      "_id": "807f1f77bcf86cd799439051",
      "milestone_id": "707f1f77bcf86cd799439041",
      "amount": 2000,
      "status": "held",
      "platform_commission": 200
    }
  ],

  "reviews": [
    {
      "_id": "907f1f77bcf86cd799439061",
      "project_id": "507f1f77bcf86cd799439013",
      "reviewer_id": "507f1f77bcf86cd799439012",
      "reviewer_type": "Client",
      "reviewed_id": "507f1f77bcf86cd799439011",
      "reviewed_type": "Freelancer",
      "rating": 5,
      "comments": "Excellent work!"
    }
  ],

  "skills": [
    {
      "_id": "607f1f77bcf86cd799439031",
      "name": "Laravel",
      "category": "software_development"
    }
  ]
}
```

### **Notas Técnicas**
1. **Relaciones Polimórficas**: En `reviews`, los campos `reviewer_id` y `reviewed_id` pueden apuntar a `clients` o `freelancers` según el tipo.
2. **Índices**: Campos como `client_id` en `projects` deben tener índices para búsquedas rápidas.
3. **Formato de Fecha**: Usar `ISODate` en MongoDB para fechas (ej: `due_date`).
4. **Paginación**: Las colecciones grandes (como `freelancers`) deben usar `skip` y `limit` en consultas.
