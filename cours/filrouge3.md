🚀 PROJET FIL ROUGE - DEMI-JOURNÉE 3 (J3 Après-midi)
SmartCity API Platform - Base de données & CRUD complet
Durée : 3h30 (13h30-17h00)
Contexte : Ce matin vous avez vu le versioning d'API, la documentation OpenAPI et HATEOAS. Cet après-midi, vous allez connecter vos services à de vraies bases de données et implémenter le CRUD complet avec validation.

📋 RAPPEL DE VOTRE SITUATION (J2 → J3)
✅ Ce que vous avez (depuis J2)

Endpoints fonctionnels avec données en dur (tableaux)
Pagination implémentée (page/limit)
Gestion d'erreurs RFC 7807 centralisée
Headers de corrélation (X-Correlation-Id)
3-5 tests unitaires qui passent
Mock Prism généré et accessible
Front qui consomme les mocks Prism

🎯 Ce qu'on vise aujourd'hui (réaliste pour 3h30)
À 17h00, chaque équipe doit avoir :

ÉQUIPES BACK-END (sauf Auth) :

✅ Base de données installée et configurée
✅ Schéma/migrations créés
✅ Endpoints CRUD complets (GET, POST, PUT, PATCH, DELETE)
✅ Validation des données entrantes (Joi/Zod/Pydantic)
✅ Tests d'intégration avec DB
✅ Remplacement des tableaux en mémoire par requêtes DB

ÉQUIPE AUTH :

✅ Table users en base de données
✅ Hash bcrypt pour les mots de passe
✅ Génération de vrais JWT signés avec clé secrète
✅ Vérification de tokens (middleware d'authentification)
✅ Tests avec utilisateurs réels en DB
✅ Middleware d'authentification exportable pour les autres équipes

ÉQUIPE FRONT :

✅ Basculer des mocks Prism vers les vrais services
✅ Formulaires de création (POST) fonctionnels
✅ Gestion des erreurs de validation côté UI
✅ Intégration avec Auth (login/register)
✅ Stockage du token (localStorage/sessionStorage)

❌ CE QUI N'EST PAS ATTENDU AUJOURD'HUI

❌ Tests exhaustifs (J6-J7)
❌ Cache Redis (J7)
❌ API Gateway (J6)
❌ Containerisation (J8)
❌ CI/CD (J8)

🏁 PHASE 1 : DAILY & ALIGNEMENT (13h30-13h45) - 15 min
Daily Stand-up par équipe (10 min)
Chaque équipe fait son stand-up interne :

Ce qui a été fait depuis J2 (lectures, préparations...)
Blockers éventuels (installation DB, choix ORM...)
Plan pour aujourd'hui

Synchronisation inter-équipes (5 min)
Point rapide tous ensemble :

Back : "Quelle base de données avez-vous choisie ?"
Auth : "Format JWT finalisé ? Middleware prêt ?"
Front : "Prêt à basculer vers les vrais services ?"

Convention d'URLs mises à jour :
AUTH      → http://localhost:3001/api/v1
EVENTS    → http://localhost:3002/api/v1
TRANSPORT → http://localhost:3003/api/v1
SERVICE_3 → http://localhost:3004/api/v1
SERVICE_4 → http://localhost:3005/api/v1
FRONT     → http://localhost:3000

📦 PHASE 2 : CONFIGURATION BASE DE DONNÉES (13h45-14h30) - 45 min
A. Installation et configuration (20 min)

Choix de base de données recommandés :
PostgreSQL (recommandé) : Robuste, ACID, JSON natif
MongoDB : Document-based, flexible
MySQL : Simple, largement utilisé
SQLite : Pour développement local uniquement

Installation rapide avec Docker :
bash# PostgreSQL
docker run --name smartcity-postgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=smartcity \
  -p 5432:5432 \
  -d postgres:15

# MongoDB
docker run --name smartcity-mongo \
  -p 27017:27017 \
  -d mongo:7

# MySQL
docker run --name smartcity-mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=smartcity \
  -p 3306:3306 \
  -d mysql:8.0

B. Configuration ORM/ODM (25 min)

Node.js - Prisma (recommandé) :
bashnpm install prisma @prisma/client
npx prisma init

# schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Event {
  id                String   @id @default(uuid())
  title             String
  description       String?
  type              EventType
  startDate         DateTime @map("start_date")
  endDate           DateTime? @map("end_date")
  location          Json     // PostgreSQL JSON
  price             Float    @default(0)
  maxAttendees      Int?     @map("max_attendees")
  currentAttendees  Int      @default(0) @map("current_attendees")
  imageUrl          String?  @map("image_url")
  tags              String[]
  createdAt         DateTime @default(now()) @map("created_at")
  updatedAt         DateTime @updatedAt @map("updated_at")
  creatorId         String   @map("creator_id")
  
  @@map("events")
}

enum EventType {
  CONCERT
  EXPOSITION
  CONFERENCE
  FESTIVAL
  SPORT
  AUTRE
}

npx prisma migrate dev --name init
npx prisma generate

Node.js - Sequelize (alternative) :
bashnpm install sequelize pg pg-hstore
npm install --save-dev sequelize-cli

# models/Event.js
const { DataTypes } = require('sequelize');

module.exports = (sequelize) => {
  const Event = sequelize.define('Event', {
    id: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true
    },
    title: {
      type: DataTypes.STRING,
      allowNull: false,
      validate: {
        len: [3, 200]
      }
    },
    description: DataTypes.TEXT,
    type: {
      type: DataTypes.ENUM,
      values: ['concert', 'exposition', 'conference', 'festival', 'sport', 'autre'],
      allowNull: false
    },
    startDate: {
      type: DataTypes.DATE,
      allowNull: false,
      field: 'start_date'
    },
    endDate: {
      type: DataTypes.DATE,
      field: 'end_date'
    },
    location: {
      type: DataTypes.JSONB, // PostgreSQL
      allowNull: false
    },
    price: {
      type: DataTypes.DECIMAL(10, 2),
      defaultValue: 0,
      validate: {
        min: 0
      }
    },
    maxAttendees: {
      type: DataTypes.INTEGER,
      field: 'max_attendees',
      validate: {
        min: 1
      }
    },
    currentAttendees: {
      type: DataTypes.INTEGER,
      defaultValue: 0,
      field: 'current_attendees',
      validate: {
        min: 0
      }
    },
    imageUrl: {
      type: DataTypes.STRING,
      field: 'image_url',
      validate: {
        isUrl: true
      }
    },
    tags: {
      type: DataTypes.ARRAY(DataTypes.STRING),
      defaultValue: []
    }
  }, {
    tableName: 'events',
    timestamps: true,
    createdAt: 'created_at',
    updatedAt: 'updated_at'
  });

  return Event;
};

Python - SQLAlchemy :
bashpip install sqlalchemy psycopg2-binary alembic

# models.py
from sqlalchemy import Column, String, DateTime, Float, Integer, JSON, ARRAY, Enum
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.dialects.postgresql import UUID
import uuid
import enum

Base = declarative_base()

class EventType(enum.Enum):
    CONCERT = "concert"
    EXPOSITION = "exposition"
    CONFERENCE = "conference"
    FESTIVAL = "festival"
    SPORT = "sport"
    AUTRE = "autre"

class Event(Base):
    __tablename__ = "events"
    
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    title = Column(String(200), nullable=False)
    description = Column(String(2000))
    type = Column(Enum(EventType), nullable=False)
    start_date = Column(DateTime, nullable=False)
    end_date = Column(DateTime)
    location = Column(JSON, nullable=False)
    price = Column(Float, default=0)
    max_attendees = Column(Integer)
    current_attendees = Column(Integer, default=0)
    image_url = Column(String)
    tags = Column(ARRAY(String))
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    creator_id = Column(String, nullable=False)

Python - FastAPI + SQLAlchemy :
bashpip install fastapi sqlalchemy psycopg2-binary alembic python-multipart

# database.py
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "postgresql://user:password@localhost/smartcity"

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

Java - Spring Data JPA :
xml<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>

// Event.java
@Entity
@Table(name = "events")
public class Event {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;
    
    @Column(nullable = false, length = 200)
    @Size(min = 3, max = 200)
    private String title;
    
    @Column(length = 2000)
    private String description;
    
    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private EventType type;
    
    @Column(name = "start_date", nullable = false)
    private LocalDateTime startDate;
    
    @Column(name = "end_date")
    private LocalDateTime endDate;
    
    @Column(columnDefinition = "jsonb")
    private String location; // JSON string
    
    @Column(precision = 10, scale = 2)
    @DecimalMin("0")
    private BigDecimal price = BigDecimal.ZERO;
    
    @Column(name = "max_attendees")
    @Min(1)
    private Integer maxAttendees;
    
    @Column(name = "current_attendees")
    @Min(0)
    private Integer currentAttendees = 0;
    
    @Column(name = "image_url")
    private String imageUrl;
    
    @ElementCollection
    @CollectionTable(name = "event_tags")
    private List<String> tags = new ArrayList<>();
    
    @CreationTimestamp
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @UpdateTimestamp
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    @Column(name = "creator_id", nullable = false)
    private String creatorId;
    
    // Getters, setters, constructors...
}

Go - GORM :
bashgo get gorm.io/gorm
go get gorm.io/driver/postgres

// models.go
package models

import (
    "time"
    "github.com/google/uuid"
    "gorm.io/gorm"
)

type EventType string

const (
    EventTypeConcert     EventType = "concert"
    EventTypeExposition  EventType = "exposition"
    EventTypeConference  EventType = "conference"
    EventTypeFestival    EventType = "festival"
    EventTypeSport       EventType = "sport"
    EventTypeAutre       EventType = "autre"
)

type Event struct {
    ID               uuid.UUID `gorm:"type:uuid;primary_key;default:uuid_generate_v4()" json:"id"`
    Title            string    `gorm:"size:200;not null" json:"title" validate:"required,min=3,max=200"`
    Description      *string   `gorm:"size:2000" json:"description"`
    Type             EventType `gorm:"not null" json:"type" validate:"required"`
    StartDate        time.Time `gorm:"not null" json:"start_date" validate:"required"`
    EndDate          *time.Time `json:"end_date"`
    Location         string    `gorm:"type:jsonb;not null" json:"location" validate:"required"`
    Price            float64   `gorm:"type:decimal(10,2);default:0" json:"price" validate:"min=0"`
    MaxAttendees     *int      `json:"max_attendees" validate:"omitempty,min=1"`
    CurrentAttendees int       `gorm:"default:0" json:"current_attendees" validate:"min=0"`
    ImageURL         *string   `json:"image_url"`
    Tags             []string  `gorm:"type:text[]" json:"tags"`
    CreatedAt        time.Time `json:"created_at"`
    UpdatedAt        time.Time `json:"updated_at"`
    CreatorID        string    `gorm:"not null" json:"creator_id" validate:"required"`
}

func (Event) TableName() string {
    return "events"
}

☕ Pause (14h30-14h45) - 15 min

🔧 PHASE 3 : CRUD COMPLET & VALIDATION (14h45-16h00) - 75 min
A. Implémentation CRUD complet (45 min)

GET /resources (liste paginée) - Mise à jour :
javascript// Node.js avec Prisma
app.get('/api/v1/events', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = Math.min(parseInt(req.query.limit) || 20, 100);
  const offset = (page - 1) * limit;
  
  // Filtres
  const where = {};
  if (req.query.type) where.type = req.query.type;
  if (req.query.city) where.location = { path: ['city'], equals: req.query.city };
  if (req.query.date_from) where.startDate = { gte: new Date(req.query.date_from) };
  if (req.query.date_to) where.startDate = { ...where.startDate, lte: new Date(req.query.date_to) };
  
  // Tri
  const orderBy = {};
  if (req.query.sort) {
    const field = req.query.sort.replace('-', '');
    orderBy[field] = req.query.sort.startsWith('-') ? 'desc' : 'asc';
  } else {
    orderBy.startDate = 'asc';
  }
  
  try {
    const [events, total] = await Promise.all([
      prisma.event.findMany({
        where,
        orderBy,
        skip: offset,
        take: limit,
        include: {
          creator: {
            select: { id: true, name: true, email: true }
          }
        }
      }),
      prisma.event.count({ where })
    ]);
    
    res.json({
      data: events,
      meta: {
        page,
        limit,
        total,
        totalPages: Math.ceil(total / limit)
      },
      links: {
        self: `/events?page=${page}&limit=${limit}`,
        first: `/events?page=1&limit=${limit}`,
        last: `/events?page=${Math.ceil(total / limit)}&limit=${limit}`,
        next: page < Math.ceil(total / limit) ? `/events?page=${page + 1}&limit=${limit}` : null,
        prev: page > 1 ? `/events?page=${page - 1}&limit=${limit}` : null
      }
    });
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Database operation failed',
      correlationId: req.correlationId
    });
  }
});

POST /resources (création) - Nouveau :
javascript// Validation avec Joi
const Joi = require('joi');

const eventSchema = Joi.object({
  title: Joi.string().min(3).max(200).required(),
  description: Joi.string().max(2000).optional(),
  type: Joi.string().valid('concert', 'exposition', 'conference', 'festival', 'sport', 'autre').required(),
  start_date: Joi.date().iso().greater('now').required(),
  end_date: Joi.date().iso().greater(Joi.ref('start_date')).optional(),
  location: Joi.object({
    name: Joi.string().required(),
    address: Joi.string().required(),
    city: Joi.string().required(),
    postal_code: Joi.string().pattern(/^\d{5}$/).optional(),
    coordinates: Joi.object({
      latitude: Joi.number().min(-90).max(90).optional(),
      longitude: Joi.number().min(-180).max(180).optional()
    }).optional()
  }).required(),
  price: Joi.number().min(0).default(0),
  max_attendees: Joi.number().integer().min(1).optional(),
  image_url: Joi.string().uri().optional(),
  tags: Joi.array().items(Joi.string()).default([])
});

app.post('/api/v1/events', authenticateToken, async (req, res) => {
  // Validation
  const { error, value } = eventSchema.validate(req.body);
  if (error) {
    return res.status(422).json({
      type: 'https://api.smartcity.local/errors/validation-error',
      title: 'Validation Failed',
      status: 422,
      detail: 'Invalid input data',
      errors: error.details.map(detail => ({
        field: detail.path.join('.'),
        code: detail.type,
        message: detail.message
      })),
      correlationId: req.correlationId
    });
  }
  
  try {
    const event = await prisma.event.create({
      data: {
        ...value,
        creatorId: req.user.id,
        startDate: new Date(value.start_date),
        endDate: value.end_date ? new Date(value.end_date) : null
      },
      include: {
        creator: {
          select: { id: true, name: true, email: true }
        }
      }
    });
    
    res.status(201)
       .header('Location', `/events/${event.id}`)
       .json(event);
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Failed to create event',
      correlationId: req.correlationId
    });
  }
});

PUT /resources/{id} (mise à jour complète) - Nouveau :
javascriptapp.put('/api/v1/events/:id', authenticateToken, async (req, res) => {
  const { id } = req.params;
  
  // Vérifier que l'événement existe
  const existingEvent = await prisma.event.findUnique({
    where: { id }
  });
  
  if (!existingEvent) {
    return res.status(404).json({
      type: 'https://api.smartcity.local/errors/not-found',
      title: 'Not Found',
      status: 404,
      detail: 'Event not found',
      correlationId: req.correlationId
    });
  }
  
  // Vérifier les permissions
  if (existingEvent.creatorId !== req.user.id && req.user.role !== 'admin') {
    return res.status(403).json({
      type: 'https://api.smartcity.local/errors/forbidden',
      title: 'Forbidden',
      status: 403,
      detail: 'You can only update your own events',
      correlationId: req.correlationId
    });
  }
  
  // Validation
  const { error, value } = eventSchema.validate(req.body);
  if (error) {
    return res.status(422).json({
      type: 'https://api.smartcity.local/errors/validation-error',
      title: 'Validation Failed',
      status: 422,
      detail: 'Invalid input data',
      errors: error.details.map(detail => ({
        field: detail.path.join('.'),
        code: detail.type,
        message: detail.message
      })),
      correlationId: req.correlationId
    });
  }
  
  try {
    const updatedEvent = await prisma.event.update({
      where: { id },
      data: {
        ...value,
        startDate: new Date(value.start_date),
        endDate: value.end_date ? new Date(value.end_date) : null
      },
      include: {
        creator: {
          select: { id: true, name: true, email: true }
        }
      }
    });
    
    res.json(updatedEvent);
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Failed to update event',
      correlationId: req.correlationId
    });
  }
});

PATCH /resources/{id} (mise à jour partielle) - Nouveau :
javascriptconst eventUpdateSchema = Joi.object({
  title: Joi.string().min(3).max(200).optional(),
  description: Joi.string().max(2000).optional(),
  type: Joi.string().valid('concert', 'exposition', 'conference', 'festival', 'sport', 'autre').optional(),
  start_date: Joi.date().iso().greater('now').optional(),
  end_date: Joi.date().iso().greater(Joi.ref('start_date')).optional(),
  location: Joi.object({
    name: Joi.string().optional(),
    address: Joi.string().optional(),
    city: Joi.string().optional(),
    postal_code: Joi.string().pattern(/^\d{5}$/).optional(),
    coordinates: Joi.object({
      latitude: Joi.number().min(-90).max(90).optional(),
      longitude: Joi.number().min(-180).max(180).optional()
    }).optional()
  }).optional(),
  price: Joi.number().min(0).optional(),
  max_attendees: Joi.number().integer().min(1).optional(),
  image_url: Joi.string().uri().optional(),
  tags: Joi.array().items(Joi.string()).optional()
});

app.patch('/api/v1/events/:id', authenticateToken, async (req, res) => {
  const { id } = req.params;
  
  // Vérifier que l'événement existe
  const existingEvent = await prisma.event.findUnique({
    where: { id }
  });
  
  if (!existingEvent) {
    return res.status(404).json({
      type: 'https://api.smartcity.local/errors/not-found',
      title: 'Not Found',
      status: 404,
      detail: 'Event not found',
      correlationId: req.correlationId
    });
  }
  
  // Vérifier les permissions
  if (existingEvent.creatorId !== req.user.id && req.user.role !== 'admin') {
    return res.status(403).json({
      type: 'https://api.smartcity.local/errors/forbidden',
      title: 'Forbidden',
      status: 403,
      detail: 'You can only update your own events',
      correlationId: req.correlationId
    });
  }
  
  // Validation
  const { error, value } = eventUpdateSchema.validate(req.body);
  if (error) {
    return res.status(422).json({
      type: 'https://api.smartcity.local/errors/validation-error',
      title: 'Validation Failed',
      status: 422,
      detail: 'Invalid input data',
      errors: error.details.map(detail => ({
        field: detail.path.join('.'),
        code: detail.type,
        message: detail.message
      })),
      correlationId: req.correlationId
    });
  }
  
  try {
    const updateData = { ...value };
    if (value.start_date) updateData.startDate = new Date(value.start_date);
    if (value.end_date) updateData.endDate = new Date(value.end_date);
    
    const updatedEvent = await prisma.event.update({
      where: { id },
      data: updateData,
      include: {
        creator: {
          select: { id: true, name: true, email: true }
        }
      }
    });
    
    res.json(updatedEvent);
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Failed to update event',
      correlationId: req.correlationId
    });
  }
});

DELETE /resources/{id} (suppression) - Nouveau :
javascriptapp.delete('/api/v1/events/:id', authenticateToken, async (req, res) => {
  const { id } = req.params;
  
  // Vérifier que l'événement existe
  const existingEvent = await prisma.event.findUnique({
    where: { id }
  });
  
  if (!existingEvent) {
    return res.status(404).json({
      type: 'https://api.smartcity.local/errors/not-found',
      title: 'Not Found',
      status: 404,
      detail: 'Event not found',
      correlationId: req.correlationId
    });
  }
  
  // Vérifier les permissions
  if (existingEvent.creatorId !== req.user.id && req.user.role !== 'admin') {
    return res.status(403).json({
      type: 'https://api.smartcity.local/errors/forbidden',
      title: 'Forbidden',
      status: 403,
      detail: 'You can only delete your own events',
      correlationId: req.correlationId
    });
  }
  
  try {
    await prisma.event.delete({
      where: { id }
    });
    
    res.status(204).send();
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Failed to delete event',
      correlationId: req.correlationId
    });
  }
});

B. Tests d'intégration avec DB (30 min)

Tests avec base de données de test :
javascript// tests/integration/events.test.js
const request = require('supertest');
const { PrismaClient } = require('@prisma/client');
const app = require('../../src/app');

const prisma = new PrismaClient({
  datasources: {
    db: {
      url: process.env.TEST_DATABASE_URL || 'postgresql://user:password@localhost:5432/smartcity_test'
    }
  }
});

describe('Events API Integration Tests', () => {
  beforeEach(async () => {
    // Nettoyer la base de test
    await prisma.event.deleteMany();
    await prisma.user.deleteMany();
  });
  
  afterAll(async () => {
    await prisma.$disconnect();
  });
  
  describe('POST /api/v1/events', () => {
    it('should create a new event with valid data', async () => {
      // Créer un utilisateur de test
      const user = await prisma.user.create({
        data: {
          email: 'test@example.com',
          name: 'Test User',
          password: 'hashedpassword'
        }
      });
      
      const eventData = {
        title: 'Test Concert',
        description: 'A test concert',
        type: 'concert',
        start_date: '2025-12-31T20:00:00Z',
        end_date: '2025-12-31T23:00:00Z',
        location: {
          name: 'Test Venue',
          address: '123 Test St',
          city: 'Test City'
        },
        price: 25.00,
        max_attendees: 100,
        tags: ['music', 'test']
      };
      
      const response = await request(app)
        .post('/api/v1/events')
        .set('Authorization', `Bearer ${generateTestToken(user.id)}`)
        .send(eventData)
        .expect(201);
      
      expect(response.body).toHaveProperty('id');
      expect(response.body.title).toBe(eventData.title);
      expect(response.body.type).toBe(eventData.type);
      expect(response.headers.location).toBe(`/events/${response.body.id}`);
      
      // Vérifier en base
      const createdEvent = await prisma.event.findUnique({
        where: { id: response.body.id }
      });
      expect(createdEvent).toBeTruthy();
      expect(createdEvent.creatorId).toBe(user.id);
    });
    
    it('should return 422 for invalid data', async () => {
      const invalidData = {
        title: 'A', // Trop court
        type: 'invalid_type',
        start_date: '2020-01-01T00:00:00Z' // Date passée
      };
      
      const response = await request(app)
        .post('/api/v1/events')
        .set('Authorization', `Bearer ${generateTestToken('user-id')}`)
        .send(invalidData)
        .expect(422);
      
      expect(response.body.type).toContain('validation-error');
      expect(response.body.errors).toHaveLength(3); // title, type, start_date
    });
  });
  
  describe('GET /api/v1/events', () => {
    it('should return paginated events', async () => {
      // Créer des événements de test
      const events = [];
      for (let i = 0; i < 25; i++) {
        events.push({
          title: `Event ${i}`,
          type: 'concert',
          startDate: new Date(`2025-12-${String(i + 1).padStart(2, '0')}T20:00:00Z`),
          location: { name: 'Venue', city: 'City' },
          creatorId: 'test-user-id'
        });
      }
      
      await prisma.event.createMany({ data: events });
      
      const response = await request(app)
        .get('/api/v1/events?page=1&limit=10')
        .expect(200);
      
      expect(response.body.data).toHaveLength(10);
      expect(response.body.meta.page).toBe(1);
      expect(response.body.meta.limit).toBe(10);
      expect(response.body.meta.total).toBe(25);
      expect(response.body.meta.totalPages).toBe(3);
    });
    
    it('should filter events by type', async () => {
      await prisma.event.createMany({
        data: [
          { title: 'Concert 1', type: 'concert', startDate: new Date('2025-12-01T20:00:00Z'), location: { name: 'Venue', city: 'City' }, creatorId: 'user1' },
          { title: 'Expo 1', type: 'exposition', startDate: new Date('2025-12-02T20:00:00Z'), location: { name: 'Venue', city: 'City' }, creatorId: 'user1' },
          { title: 'Concert 2', type: 'concert', startDate: new Date('2025-12-03T20:00:00Z'), location: { name: 'Venue', city: 'City' }, creatorId: 'user1' }
        ]
      });
      
      const response = await request(app)
        .get('/api/v1/events?type=concert')
        .expect(200);
      
      expect(response.body.data).toHaveLength(2);
      expect(response.body.data.every(event => event.type === 'concert')).toBe(true);
    });
  });
  
  describe('PUT /api/v1/events/:id', () => {
    it('should update an event completely', async () => {
      const user = await prisma.user.create({
        data: { email: 'test@example.com', name: 'Test User', password: 'hash' }
      });
      
      const event = await prisma.event.create({
        data: {
          title: 'Original Title',
          type: 'concert',
          startDate: new Date('2025-12-31T20:00:00Z'),
          location: { name: 'Original Venue', city: 'Original City' },
          creatorId: user.id
        }
      });
      
      const updateData = {
        title: 'Updated Title',
        description: 'Updated description',
        type: 'exposition',
        start_date: '2025-12-31T10:00:00Z',
        end_date: '2025-12-31T18:00:00Z',
        location: {
          name: 'Updated Venue',
          address: '456 Updated St',
          city: 'Updated City'
        },
        price: 15.00,
        max_attendees: 50
      };
      
      const response = await request(app)
        .put(`/api/v1/events/${event.id}`)
        .set('Authorization', `Bearer ${generateTestToken(user.id)}`)
        .send(updateData)
        .expect(200);
      
      expect(response.body.title).toBe(updateData.title);
      expect(response.body.type).toBe(updateData.type);
      expect(response.body.price).toBe(updateData.price);
    });
  });
  
  describe('DELETE /api/v1/events/:id', () => {
    it('should delete an event', async () => {
      const user = await prisma.user.create({
        data: { email: 'test@example.com', name: 'Test User', password: 'hash' }
      });
      
      const event = await prisma.event.create({
        data: {
          title: 'Event to Delete',
          type: 'concert',
          startDate: new Date('2025-12-31T20:00:00Z'),
          location: { name: 'Venue', city: 'City' },
          creatorId: user.id
        }
      });
      
      await request(app)
        .delete(`/api/v1/events/${event.id}`)
        .set('Authorization', `Bearer ${generateTestToken(user.id)}`)
        .expect(204);
      
      // Vérifier que l'événement a été supprimé
      const deletedEvent = await prisma.event.findUnique({
        where: { id: event.id }
      });
      expect(deletedEvent).toBeNull();
    });
  });
});

function generateTestToken(userId) {
  // Générer un token JWT de test
  const jwt = require('jsonwebtoken');
  return jwt.sign(
    { sub: userId, email: 'test@example.com', role: 'user' },
    process.env.JWT_SECRET || 'test-secret',
    { expiresIn: '1h' }
  );
}

🔐 PHASE 4 : ÉQUIPE AUTH - JWT RÉELS (14h45-16h00) - Parallel
Pendant que les autres équipes font le CRUD, l'équipe AUTH implémente la vraie authentification

A. Hash des mots de passe avec bcrypt (20 min)

Node.js :
bashnpm install bcryptjs
npm install --save-dev @types/bcryptjs

// auth/utils.js
const bcrypt = require('bcryptjs');

const SALT_ROUNDS = 12; // Recommandé pour 2024

async function hashPassword(password) {
  return await bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(password, hashedPassword) {
  return await bcrypt.compare(password, hashedPassword);
}

module.exports = { hashPassword, verifyPassword };

// routes/auth.js
const { hashPassword, verifyPassword } = require('../utils/auth');

router.post('/auth/register', async (req, res) => {
  const { email, password, name } = req.body;
  
  // Validation
  if (!email || !password || !name) {
    return problem422(req, res, "Email, password and name are required");
  }
  
  if (password.length < 8) {
    return problem422(req, res, "Password must be at least 8 characters long");
  }
  
  // Vérifier si email existe déjà
  const existingUser = await prisma.user.findUnique({
    where: { email }
  });
  
  if (existingUser) {
    return problem409(req, res, "Email already registered");
  }
  
  try {
    // Hash du mot de passe
    const hashedPassword = await hashPassword(password);
    
    // Créer l'utilisateur
    const user = await prisma.user.create({
      data: {
        email,
        password: hashedPassword,
        name,
        role: 'user'
      },
      select: {
        id: true,
        email: true,
        name: true,
        role: true,
        createdAt: true
      }
    });
    
    res.status(201).json(user);
  } catch (error) {
    console.error('Registration error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Failed to create user account',
      correlationId: req.correlationId
    });
  }
});

router.post('/auth/login', async (req, res) => {
  const { email, password } = req.body;
  
  // Validation
  if (!email || !password) {
    return problem422(req, res, "Email and password are required");
  }
  
  try {
    // Trouver l'utilisateur
    const user = await prisma.user.findUnique({
      where: { email }
    });
    
    if (!user) {
      return problem401(req, res, "Invalid credentials");
    }
    
    // Vérifier le mot de passe
    const isValidPassword = await verifyPassword(password, user.password);
    if (!isValidPassword) {
      return problem401(req, res, "Invalid credentials");
    }
    
    // Générer les tokens JWT
    const accessToken = jwt.sign(
      {
        sub: user.id,
        email: user.email,
        name: user.name,
        role: user.role
      },
      process.env.JWT_SECRET,
      { expiresIn: '1h' }
    );
    
    const refreshToken = jwt.sign(
      {
        sub: user.id,
        type: 'refresh'
      },
      process.env.JWT_REFRESH_SECRET,
      { expiresIn: '7d' }
    );
    
    // Stocker le refresh token en base (optionnel mais recommandé)
    await prisma.refreshToken.create({
      data: {
        token: refreshToken,
        userId: user.id,
        expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
      }
    });
    
    res.json({
      accessToken,
      refreshToken,
      expiresIn: 3600,
      tokenType: 'Bearer',
      user: {
        id: user.id,
        email: user.email,
        name: user.name,
        role: user.role
      }
    });
  } catch (error) {
    console.error('Login error:', error);
    res.status(500).json({
      type: 'https://api.smartcity.local/errors/internal-error',
      title: 'Internal Server Error',
      status: 500,
      detail: 'Authentication failed',
      correlationId: req.correlationId
    });
  }
});

Python :
bashpip install bcrypt python-jose[cryptography] passlib[bcrypt]

# auth/utils.py
import bcrypt
from jose import JWTError, jwt
from datetime import datetime, timedelta
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def create_access_token(data: dict, expires_delta: timedelta = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(hours=1)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

# auth/routes.py
from fastapi import APIRouter, Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

router = APIRouter()
security = HTTPBearer()

@router.post("/register")
async def register(user_data: UserCreate, db: Session = Depends(get_db)):
    # Vérifier si l'utilisateur existe déjà
    existing_user = db.query(User).filter(User.email == user_data.email).first()
    if existing_user:
        raise HTTPException(
            status_code=status.HTTP_409_CONFLICT,
            detail="Email already registered"
        )
    
    # Créer l'utilisateur avec mot de passe hashé
    hashed_password = hash_password(user_data.password)
    user = User(
        email=user_data.email,
        password=hashed_password,
        name=user_data.name,
        role="user"
    )
    
    db.add(user)
    db.commit()
    db.refresh(user)
    
    return {
        "id": user.id,
        "email": user.email,
        "name": user.name,
        "role": user.role
    }

@router.post("/login")
async def login(login_data: UserLogin, db: Session = Depends(get_db)):
    # Trouver l'utilisateur
    user = db.query(User).filter(User.email == login_data.email).first()
    if not user or not verify_password(login_data.password, user.password):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid credentials"
        )
    
    # Créer les tokens
    access_token = create_access_token(
        data={"sub": str(user.id), "email": user.email, "role": user.role}
    )
    
    return {
        "accessToken": access_token,
        "tokenType": "Bearer",
        "expiresIn": 3600,
        "user": {
            "id": user.id,
            "email": user.email,
            "name": user.name,
            "role": user.role
        }
    }

B. Middleware d'authentification exportable (25 min)

Node.js - Middleware réutilisable :
javascript// middleware/auth.js
const jwt = require('jsonwebtoken');
const { PrismaClient } = require('@prisma/client');

const prisma = new PrismaClient();

async function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1]; // Bearer TOKEN
  
  if (!token) {
    return res.status(401).json({
      type: 'https://api.smartcity.local/errors/unauthorized',
      title: 'Unauthorized',
      status: 401,
      detail: 'Access token required',
      correlationId: req.correlationId
    });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // Optionnel : vérifier que l'utilisateur existe encore
    const user = await prisma.user.findUnique({
      where: { id: decoded.sub },
      select: {
        id: true,
        email: true,
        name: true,
        role: true,
        isActive: true
      }
    });
    
    if (!user || !user.isActive) {
      return res.status(401).json({
        type: 'https://api.smartcity.local/errors/unauthorized',
        title: 'Unauthorized',
        status: 401,
        detail: 'User not found or inactive',
        correlationId: req.correlationId
      });
    }
    
    req.user = user;
    next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') {
      return res.status(401).json({
        type: 'https://api.smartcity.local/errors/unauthorized',
        title: 'Unauthorized',
        status: 401,
        detail: 'Token expired',
        correlationId: req.correlationId
      });
    }
    
    return res.status(401).json({
      type: 'https://api.smartcity.local/errors/unauthorized',
      title: 'Unauthorized',
      status: 401,
      detail: 'Invalid token',
      correlationId: req.correlationId
    });
  }
}

// Middleware optionnel (pour les endpoints publics avec données enrichies si connecté)
async function authenticateOptional(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    req.user = null;
    return next();
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const user = await prisma.user.findUnique({
      where: { id: decoded.sub },
      select: { id: true, email: true, name: true, role: true }
    });
    
    req.user = user;
  } catch (error) {
    req.user = null;
  }
  
  next();
}

// Middleware de vérification de rôle
function requireRole(roles) {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({
        type: 'https://api.smartcity.local/errors/unauthorized',
        title: 'Unauthorized',
        status: 401,
        detail: 'Authentication required',
        correlationId: req.correlationId
      });
    }
    
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        type: 'https://api.smartcity.local/errors/forbidden',
        title: 'Forbidden',
        status: 403,
        detail: 'Insufficient permissions',
        correlationId: req.correlationId
      });
    }
    
    next();
  };
}

module.exports = {
  authenticateToken,
  authenticateOptional,
  requireRole
};

// Utilisation dans les autres services
const { authenticateToken, requireRole } = require('@smartcity/auth-middleware');

app.post('/api/v1/events', authenticateToken, async (req, res) => {
  // req.user est disponible
  const event = await createEvent(req.body, req.user.id);
  res.json(event);
});

app.delete('/api/v1/events/:id', authenticateToken, requireRole(['admin']), async (req, res) => {
  // Seuls les admins peuvent supprimer
});

Python - Middleware FastAPI :
python# auth/middleware.py
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import JWTError, jwt
from sqlalchemy.orm import Session

security = HTTPBearer()

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security),
    db: Session = Depends(get_db)
) -> User:
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    
    try:
        payload = jwt.decode(credentials.credentials, SECRET_KEY, algorithms=[ALGORITHM])
        user_id: str = payload.get("sub")
        if user_id is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception
    
    user = db.query(User).filter(User.id == user_id).first()
    if user is None:
        raise credentials_exception
    
    return user

async def get_current_active_user(current_user: User = Depends(get_current_user)) -> User:
    if not current_user.is_active:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user

def require_role(required_roles: list):
    def role_checker(current_user: User = Depends(get_current_active_user)):
        if current_user.role not in required_roles:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="Not enough permissions"
            )
        return current_user
    return role_checker

# Utilisation
@app.post("/events")
async def create_event(
    event: EventCreate,
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    # current_user est disponible
    pass

@app.delete("/events/{event_id}")
async def delete_event(
    event_id: str,
    current_user: User = Depends(require_role(["admin"])),
    db: Session = Depends(get_db)
):
    # Seuls les admins peuvent supprimer
    pass

C. Tests d'authentification (15 min)

Tests complets de l'authentification :
javascript// tests/auth.test.js
const request = require('supertest');
const app = require('../src/app');
const { PrismaClient } = require('@prisma/client');

const prisma = new PrismaClient();

describe('Authentication Tests', () => {
  beforeEach(async () => {
    await prisma.user.deleteMany();
  });
  
  afterAll(async () => {
    await prisma.$disconnect();
  });
  
  describe('POST /auth/register', () => {
    it('should register a new user with hashed password', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'securepassword123',
        name: 'Test User'
      };
      
      const response = await request(app)
        .post('/api/v1/auth/register')
        .send(userData)
        .expect(201);
      
      expect(response.body).toHaveProperty('id');
      expect(response.body.email).toBe(userData.email);
      expect(response.body.name).toBe(userData.name);
      expect(response.body).not.toHaveProperty('password');
      
      // Vérifier que le mot de passe est hashé en base
      const user = await prisma.user.findUnique({
        where: { email: userData.email }
      });
      expect(user.password).not.toBe(userData.password);
      expect(user.password).toMatch(/^\$2[aby]\$\d+\$/); // Format bcrypt
    });
    
    it('should reject weak passwords', async () => {
      const userData = {
        email: 'test@example.com',
        password: '123', // Trop court
        name: 'Test User'
      };
      
      const response = await request(app)
        .post('/api/v1/auth/register')
        .send(userData)
        .expect(422);
      
      expect(response.body.detail).toContain('at least 8 characters');
    });
    
    it('should reject duplicate emails', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'securepassword123',
        name: 'Test User'
      };
      
      // Premier enregistrement
      await request(app)
        .post('/api/v1/auth/register')
        .send(userData)
        .expect(201);
      
      // Tentative de doublon
      const response = await request(app)
        .post('/api/v1/auth/register')
        .send(userData)
        .expect(409);
      
      expect(response.body.detail).toContain('already registered');
    });
  });
  
  describe('POST /auth/login', () => {
    beforeEach(async () => {
      // Créer un utilisateur de test
      const bcrypt = require('bcryptjs');
      await prisma.user.create({
        data: {
          email: 'test@example.com',
          password: await bcrypt.hash('securepassword123', 12),
          name: 'Test User',
          role: 'user'
        }
      });
    });
    
    it('should login with valid credentials', async () => {
      const loginData = {
        email: 'test@example.com',
        password: 'securepassword123'
      };
      
      const response = await request(app)
        .post('/api/v1/auth/login')
        .send(loginData)
        .expect(200);
      
      expect(response.body).toHaveProperty('accessToken');
      expect(response.body).toHaveProperty('refreshToken');
      expect(response.body.tokenType).toBe('Bearer');
      expect(response.body.expiresIn).toBe(3600);
      expect(response.body.user.email).toBe(loginData.email);
      
      // Vérifier que le token est valide
      const jwt = require('jsonwebtoken');
      const decoded = jwt.verify(response.body.accessToken, process.env.JWT_SECRET);
      expect(decoded.sub).toBeTruthy();
      expect(decoded.email).toBe(loginData.email);
    });
    
    it('should reject invalid credentials', async () => {
      const loginData = {
        email: 'test@example.com',
        password: 'wrongpassword'
      };
      
      const response = await request(app)
        .post('/api/v1/auth/login')
        .send(loginData)
        .expect(401);
      
      expect(response.body.detail).toContain('Invalid credentials');
    });
  });
  
  describe('Protected endpoints', () => {
    let accessToken;
    
    beforeEach(async () => {
      // Créer un utilisateur et obtenir un token
      const bcrypt = require('bcryptjs');
      const user = await prisma.user.create({
        data: {
          email: 'test@example.com',
          password: await bcrypt.hash('securepassword123', 12),
          name: 'Test User',
          role: 'user'
        }
      });
      
      const loginResponse = await request(app)
        .post('/api/v1/auth/login')
        .send({
          email: 'test@example.com',
          password: 'securepassword123'
        });
      
      accessToken = loginResponse.body.accessToken;
    });
    
    it('should access protected endpoint with valid token', async () => {
      const response = await request(app)
        .get('/api/v1/users/me')
        .set('Authorization', `Bearer ${accessToken}`)
        .expect(200);
      
      expect(response.body.email).toBe('test@example.com');
    });
    
    it('should reject requests without token', async () => {
      await request(app)
        .get('/api/v1/users/me')
        .expect(401);
    });
    
    it('should reject requests with invalid token', async () => {
      await request(app)
        .get('/api/v1/users/me')
        .set('Authorization', 'Bearer invalid-token')
        .expect(401);
    });
    
    it('should reject requests with expired token', async () => {
      const jwt = require('jsonwebtoken');
      const expiredToken = jwt.sign(
        { sub: 'user-id', exp: Math.floor(Date.now() / 1000) - 3600 }, // Expiré il y a 1h
        process.env.JWT_SECRET
      );
      
      await request(app)
        .get('/api/v1/users/me')
        .set('Authorization', `Bearer ${expiredToken}`)
        .expect(401);
    });
  });
});

🎨 PHASE 5 : INTÉGRATION FRONT (15h00-16h15) - Parallel
Cette phase est spécifique à l'équipe FRONT

A. Basculer vers les vrais services (30 min)

Configuration pour basculer des mocks vers les vrais services :
javascript// front/src/config/api.js

const USE_MOCKS = process.env.REACT_APP_USE_MOCKS === 'true';

const API_BASE_URLS = {
  // URLs des vrais services
  events: USE_MOCKS ? 'http://localhost:4020' : 'http://localhost:3002/api/v1',
  transport: USE_MOCKS ? 'http://localhost:4030' : 'http://localhost:3003/api/v1',
  auth: USE_MOCKS ? 'http://localhost:4010' : 'http://localhost:3001/api/v1',
  // Ajoutez les autres services
};

export default API_BASE_URLS;

// front/src/api/client.js
import API_BASE_URLS from '../config/api';

class ApiClient {
  constructor() {
    this.baseUrls = API_BASE_URLS;
    this.authToken = localStorage.getItem('authToken');
  }
  
  setAuthToken(token) {
    this.authToken = token;
    localStorage.setItem('authToken', token);
  }
  
  clearAuthToken() {
    this.authToken = null;
    localStorage.removeItem('authToken');
  }
  
  async request(service, endpoint, options = {}) {
    const baseUrl = this.baseUrls[service];
    const url = `${baseUrl}${endpoint}`;
    
    const headers = {
      'Accept': 'application/json',
      'Content-Type': 'application/json',
      'X-Correlation-Id': this.generateUUID(),
      ...options.headers
    };
    
    // Ajouter le token d'authentification si disponible
    if (this.authToken) {
      headers['Authorization'] = `Bearer ${this.authToken}`;
    }
    
    const config = {
      method: options.method || 'GET',
      headers,
      ...options
    };
    
    if (options.body && typeof options.body === 'object') {
      config.body = JSON.stringify(options.body);
    }
    
    try {
      const response = await fetch(url, config);
      
      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        throw new ApiError(errorData, response.status);
      }
      
      return await response.json();
    } catch (error) {
      if (error instanceof ApiError) {
        throw error;
      }
      throw new ApiError({ detail: 'Network error' }, 0);
    }
  }
  
  generateUUID() {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
      const r = Math.random() * 16 | 0;
      const v = c === 'x' ? r : (r & 0x3 | 0x8);
      return v.toString(16);
    });
  }
}

class ApiError extends Error {
  constructor(problem, status) {
    super(problem.detail || 'API Error');
    this.problem = problem;
    this.status = status;
  }
}

export const apiClient = new ApiClient();
export { ApiError };

B. Formulaires de création (POST) (30 min)

Composant de création d'événement :
javascript// front/src/components/EventForm.jsx
import React, { useState } from 'react';
import { apiClient } from '../api/client';

export function EventForm({ onEventCreated, onCancel }) {
  const [formData, setFormData] = useState({
    title: '',
    description: '',
    type: 'concert',
    start_date: '',
    end_date: '',
    location: {
      name: '',
      address: '',
      city: '',
      postal_code: ''
    },
    price: 0,
    max_attendees: '',
    image_url: '',
    tags: []
  });
  
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const handleChange = (field, value) => {
    if (field.includes('.')) {
      const [parent, child] = field.split('.');
      setFormData(prev => ({
        ...prev,
        [parent]: {
          ...prev[parent],
          [child]: value
        }
      }));
    } else {
      setFormData(prev => ({
        ...prev,
        [field]: value
      }));
    }
    
    // Effacer l'erreur du champ modifié
    if (errors[field]) {
      setErrors(prev => ({
        ...prev,
        [field]: null
      }));
    }
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);
    setErrors({});
    
    try {
      // Préparer les données
      const submitData = {
        ...formData,
        start_date: new Date(formData.start_date).toISOString(),
        end_date: formData.end_date ? new Date(formData.end_date).toISOString() : undefined,
        price: parseFloat(formData.price),
        max_attendees: formData.max_attendees ? parseInt(formData.max_attendees) : undefined,
        tags: formData.tags.filter(tag => tag.trim())
      };
      
      const event = await apiClient.request('events', '/events', {
        method: 'POST',
        body: submitData
      });
      
      onEventCreated(event);
    } catch (error) {
      if (error.status === 422 && error.problem.errors) {
        // Erreurs de validation
        const validationErrors = {};
        error.problem.errors.forEach(err => {
          validationErrors[err.field] = err.message;
        });
        setErrors(validationErrors);
      } else {
        // Erreur générale
        setErrors({ general: error.problem.detail || 'Erreur lors de la création' });
      }
    } finally {
      setIsSubmitting(false);
    }
  };
  
  return (
    <form onSubmit={handleSubmit} className="event-form">
      <h2>Créer un événement</h2>
      
      {errors.general && (
        <div className="error-message">
          {errors.general}
        </div>
      )}
      
      <div className="form-group">
        <label htmlFor="title">Titre *</label>
        <input
          type="text"
          id="title"
          value={formData.title}
          onChange={(e) => handleChange('title', e.target.value)}
          className={errors.title ? 'error' : ''}
          required
        />
        {errors.title && <span className="error-text">{errors.title}</span>}
      </div>
      
      <div className="form-group">
        <label htmlFor="description">Description</label>
        <textarea
          id="description"
          value={formData.description}
          onChange={(e) => handleChange('description', e.target.value)}
          rows={4}
        />
      </div>
      
      <div className="form-group">
        <label htmlFor="type">Type *</label>
        <select
          id="type"
          value={formData.type}
          onChange={(e) => handleChange('type', e.target.value)}
          required
        >
          <option value="concert">Concert</option>
          <option value="exposition">Exposition</option>
          <option value="conference">Conférence</option>
          <option value="festival">Festival</option>
          <option value="sport">Sport</option>
          <option value="autre">Autre</option>
        </select>
      </div>
      
      <div className="form-row">
        <div className="form-group">
          <label htmlFor="start_date">Date de début *</label>
          <input
            type="datetime-local"
            id="start_date"
            value={formData.start_date}
            onChange={(e) => handleChange('start_date', e.target.value)}
            className={errors.start_date ? 'error' : ''}
            required
          />
          {errors.start_date && <span className="error-text">{errors.start_date}</span>}
        </div>
        
        <div className="form-group">
          <label htmlFor="end_date">Date de fin</label>
          <input
            type="datetime-local"
            id="end_date"
            value={formData.end_date}
            onChange={(e) => handleChange('end_date', e.target.value)}
          />
        </div>
      </div>
      
      <div className="form-group">
        <label htmlFor="location.name">Nom du lieu *</label>
        <input
          type="text"
          id="location.name"
          value={formData.location.name}
          onChange={(e) => handleChange('location.name', e.target.value)}
          className={errors['location.name'] ? 'error' : ''}
          required
        />
        {errors['location.name'] && <span className="error-text">{errors['location.name']}</span>}
      </div>
      
      <div className="form-group">
        <label htmlFor="location.address">Adresse *</label>
        <input
          type="text"
          id="location.address"
          value={formData.location.address}
          onChange={(e) => handleChange('location.address', e.target.value)}
          className={errors['location.address'] ? 'error' : ''}
          required
        />
        {errors['location.address'] && <span className="error-text">{errors['location.address']}</span>}
      </div>
      
      <div className="form-row">
        <div className="form-group">
          <label htmlFor="location.city">Ville *</label>
          <input
            type="text"
            id="location.city"
            value={formData.location.city}
            onChange={(e) => handleChange('location.city', e.target.value)}
            className={errors['location.city'] ? 'error' : ''}
            required
          />
          {errors['location.city'] && <span className="error-text">{errors['location.city']}</span>}
        </div>
        
        <div className="form-group">
          <label htmlFor="location.postal_code">Code postal</label>
          <input
            type="text"
            id="location.postal_code"
            value={formData.location.postal_code}
            onChange={(e) => handleChange('location.postal_code', e.target.value)}
            pattern="[0-9]{5}"
            placeholder="31000"
          />
        </div>
      </div>
      
      <div className="form-row">
        <div className="form-group">
          <label htmlFor="price">Prix (€)</label>
          <input
            type="number"
            id="price"
            value={formData.price}
            onChange={(e) => handleChange('price', e.target.value)}
            min="0"
            step="0.01"
          />
        </div>
        
        <div className="form-group">
          <label htmlFor="max_attendees">Nombre max de participants</label>
          <input
            type="number"
            id="max_attendees"
            value={formData.max_attendees}
            onChange={(e) => handleChange('max_attendees', e.target.value)}
            min="1"
          />
        </div>
      </div>
      
      <div className="form-group">
        <label htmlFor="image_url">URL de l'image</label>
        <input
          type="url"
          id="image_url"
          value={formData.image_url}
          onChange={(e) => handleChange('image_url', e.target.value)}
          placeholder="https://example.com/image.jpg"
        />
      </div>
      
      <div className="form-group">
        <label htmlFor="tags">Tags (séparés par des virgules)</label>
        <input
          type="text"
          id="tags"
          value={formData.tags.join(', ')}
          onChange={(e) => handleChange('tags', e.target.value.split(',').map(tag => tag.trim()))}
          placeholder="musique, jazz, extérieur"
        />
      </div>
      
      <div className="form-actions">
        <button type="button" onClick={onCancel} disabled={isSubmitting}>
          Annuler
        </button>
        <button type="submit" disabled={isSubmitting}>
          {isSubmitting ? 'Création...' : 'Créer l\'événement'}
        </button>
      </div>
    </form>
  );
}

C. Gestion des erreurs de validation côté UI (15 min)

Composant d'affichage des erreurs :
javascript// front/src/components/ErrorDisplay.jsx
import React from 'react';

export function ErrorDisplay({ error, onRetry }) {
  if (!error) return null;
  
  // Erreur RFC 7807
  if (error.problem) {
    return (
      <div className="error-card">
        <div className="error-header">
          <h3>{error.problem.title}</h3>
          <span className="error-status">{error.problem.status}</span>
        </div>
        
        <p className="error-detail">{error.problem.detail}</p>
        
        {error.problem.errors && error.problem.errors.length > 0 && (
          <div className="validation-errors">
            <h4>Erreurs de validation :</h4>
            <ul>
              {error.problem.errors.map((err, index) => (
                <li key={index}>
                  <strong>{err.field}</strong>: {err.message}
                </li>
              ))}
            </ul>
          </div>
        )}
        
        {error.problem.correlationId && (
          <small className="correlation-id">
            ID de corrélation: {error.problem.correlationId}
          </small>
        )}
        
        {onRetry && (
          <button onClick={onRetry} className="retry-button">
            Réessayer
          </button>
        )}
      </div>
    );
  }
  
  // Erreur générique
  return (
    <div className="error-card">
      <p>{error.message || 'Une erreur est survenue'}</p>
      {onRetry && (
        <button onClick={onRetry} className="retry-button">
          Réessayer
        </button>
      )}
    </div>
  );
}

D. Intégration avec Auth (login/register) (15 min)

Composant d'authentification :
javascript// front/src/components/AuthForm.jsx
import React, { useState } from 'react';
import { apiClient } from '../api/client';

export function AuthForm({ onAuthSuccess, onCancel }) {
  const [mode, setMode] = useState('login'); // 'login' ou 'register'
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    name: ''
  });
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const handleChange = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
    
    if (errors[field]) {
      setErrors(prev => ({
        ...prev,
        [field]: null
      }));
    }
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);
    setErrors({});
    
    try {
      if (mode === 'login') {
        const response = await apiClient.request('auth', '/auth/login', {
          method: 'POST',
          body: {
            email: formData.email,
            password: formData.password
          }
        });
        
        // Stocker le token
        apiClient.setAuthToken(response.accessToken);
        
        onAuthSuccess(response.user);
      } else {
        const response = await apiClient.request('auth', '/auth/register', {
          method: 'POST',
          body: {
            email: formData.email,
            password: formData.password,
            name: formData.name
          }
        });
        
        // Après inscription, se connecter automatiquement
        const loginResponse = await apiClient.request('auth', '/auth/login', {
          method: 'POST',
          body: {
            email: formData.email,
            password: formData.password
          }
        });
        
        apiClient.setAuthToken(loginResponse.accessToken);
        onAuthSuccess(loginResponse.user);
      }
    } catch (error) {
      if (error.status === 422 && error.problem.errors) {
        const validationErrors = {};
        error.problem.errors.forEach(err => {
          validationErrors[err.field] = err.message;
        });
        setErrors(validationErrors);
      } else {
        setErrors({ general: error.problem.detail || 'Erreur d\'authentification' });
      }
    } finally {
      setIsSubmitting(false);
    }
  };
  
  return (
    <form onSubmit={handleSubmit} className="auth-form">
      <h2>{mode === 'login' ? 'Connexion' : 'Inscription'}</h2>
      
      {errors.general && (
        <div className="error-message">
          {errors.general}
        </div>
      )}
      
      <div className="form-group">
        <label htmlFor="email">Email *</label>
        <input
          type="email"
          id="email"
          value={formData.email}
          onChange={(e) => handleChange('email', e.target.value)}
          className={errors.email ? 'error' : ''}
          required
        />
        {errors.email && <span className="error-text">{errors.email}</span>}
      </div>
      
      <div className="form-group">
        <label htmlFor="password">Mot de passe *</label>
        <input
          type="password"
          id="password"
          value={formData.password}
          onChange={(e) => handleChange('password', e.target.value)}
          className={errors.password ? 'error' : ''}
          required
        />
        {errors.password && <span className="error-text">{errors.password}</span>}
      </div>
      
      {mode === 'register' && (
        <div className="form-group">
          <label htmlFor="name">Nom complet *</label>
          <input
            type="text"
            id="name"
            value={formData.name}
            onChange={(e) => handleChange('name', e.target.value)}
            className={errors.name ? 'error' : ''}
            required
          />
          {errors.name && <span className="error-text">{errors.name}</span>}
        </div>
      )}
      
      <div className="form-actions">
        <button type="button" onClick={onCancel} disabled={isSubmitting}>
          Annuler
        </button>
        <button type="submit" disabled={isSubmitting}>
          {isSubmitting ? 'Traitement...' : (mode === 'login' ? 'Se connecter' : 'S\'inscrire')}
        </button>
      </div>
      
      <div className="auth-switch">
        {mode === 'login' ? (
          <p>
            Pas encore de compte ?{' '}
            <button type="button" onClick={() => setMode('register')}>
              S'inscrire
            </button>
          </p>
        ) : (
          <p>
            Déjà un compte ?{' '}
            <button type="button" onClick={() => setMode('login')}>
              Se connecter
            </button>
          </p>
        )}
      </div>
    </form>
  );
}

🎤 PHASE 6 : DÉMO & SYNCHRONISATION (16h15-17h00) - 45 min
Format de démo par équipe (5-6 min chacune)

ÉQUIPES BACK-END (sauf Auth)
1. Démo live des endpoints CRUD (3 min)

# Test avec curl ou Postman

# GET liste avec filtres
curl "http://localhost:3002/api/v1/events?type=concert&city=Toulouse&page=1&limit=5"

# POST création
curl -X POST http://localhost:3002/api/v1/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "title": "Nouveau Concert",
    "type": "concert",
    "start_date": "2025-12-31T20:00:00Z",
    "location": {
      "name": "Nouveau Lieu",
      "address": "123 Nouvelle Rue",
      "city": "Toulouse"
    },
    "price": 20.00
  }'

# PUT mise à jour complète
curl -X PUT http://localhost:3002/api/v1/events/EVENT_ID \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "title": "Concert Modifié",
    "type": "concert",
    "start_date": "2025-12-31T20:00:00Z",
    "location": {
      "name": "Lieu Modifié",
      "address": "456 Rue Modifiée",
      "city": "Toulouse"
    },
    "price": 25.00
  }'

# PATCH mise à jour partielle
curl -X PATCH http://localhost:3002/api/v1/events/EVENT_ID \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "price": 30.00,
    "max_attendees": 150
  }'

# DELETE suppression
curl -X DELETE http://localhost:3002/api/v1/events/EVENT_ID \
  -H "Authorization: Bearer YOUR_TOKEN"

2. Montrer la base de données (1 min)

# Connexion à la base
psql -h localhost -U postgres -d smartcity

# Vérifier les données
SELECT id, title, type, created_at FROM events LIMIT 5;

3. Montrer les tests d'intégration (1 min)

npm test
# Montrer que les tests passent avec la vraie DB

4. Montrer la validation (1 min)

# Test avec données invalides
curl -X POST http://localhost:3002/api/v1/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "title": "A",
    "type": "invalid_type",
    "start_date": "2020-01-01T00:00:00Z"
  }'

# Montrer la réponse 422 avec détails des erreurs

ÉQUIPE AUTH
1. Démo des endpoints d'authentification (3 min)

# Register
curl -X POST http://localhost:3001/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "securepassword123",
    "name": "Test User"
  }'

# Login
curl -X POST http://localhost:3001/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "securepassword123"
  }'

# Utiliser le token pour accéder à un endpoint protégé
curl http://localhost:3001/api/v1/users/me \
  -H "Authorization: Bearer ACCESS_TOKEN"

2. Montrer le hash des mots de passe (1 min)

# Connexion à la base
psql -h localhost -U postgres -d smartcity

# Vérifier que les mots de passe sont hashés
SELECT email, password FROM users LIMIT 3;

# Montrer le format bcrypt ($2a$12$...)

3. Décodage du JWT (1 min)

# Copier un token de la réponse login
# Aller sur https://jwt.io
# Coller le token et montrer le payload décodé

4. Middleware d'authentification (1 min)

# Montrer comment le middleware est utilisé dans les autres services
# Exemple dans events service
const { authenticateToken } = require('@smartcity/auth-middleware');

app.post('/api/v1/events', authenticateToken, async (req, res) => {
  // req.user est disponible
});

ÉQUIPE FRONT
1. Démo de l'application avec vrais services (2 min)

Ouvrir l'application dans le navigateur
Montrer la liste d'événements (depuis la vraie DB)
Naviguer entre les pages (pagination)
Montrer les filtres fonctionnels

2. Démo des formulaires de création (2 min)

Cliquer sur "Créer un événement"
Remplir le formulaire
Soumettre et montrer la création réussie
Montrer la gestion des erreurs de validation

3. Démo de l'authentification (1 min)

Cliquer sur "Se connecter"
Saisir les identifiants
Montrer la connexion réussie
Montrer que les actions protégées sont maintenant disponibles

4. DevTools - Requêtes réelles (1 min)

Ouvrir DevTools → Network
Faire une requête
Montrer les headers Authorization
Montrer les réponses avec vraies données

Discussion collective (15 min)
Points à valider tous ensemble :

1. Compatibilité des contrats (5 min)

Back : "Les réponses correspondent-elles à l'OpenAPI ?"
Auth : "Le middleware fonctionne-t-il avec tous les services ?"
Front : "Les données reçues correspondent-elles aux attentes ?"

2. Performance et stabilité (5 min)

Quels problèmes de performance avez-vous rencontrés ?
La base de données répond-elle assez vite ?
Y a-t-il des optimisations à faire ?

3. Préparation J4 (5 min)

Ce qui arrive demain :
Sécurité avancée : Rate limiting, CORS, headers de sécurité
RBAC (Role-Based Access Control) : Permissions granulaires
Audit et logging : Traçabilité des actions

Questions à l'enseignant :

Comment optimiser les requêtes DB ?
Quelle stratégie de cache recommandez-vous ?
Comment gérer les migrations en production ?

✅ CHECKLIST DE FIN DE JOURNÉE
Chaque équipe BACK vérifie :

✅ Base de données installée et configurée
✅ Schéma/migrations créés et appliqués
✅ Endpoints CRUD complets (GET, POST, PUT, PATCH, DELETE)
✅ Validation des données entrantes fonctionnelle
✅ Tests d'intégration avec DB qui passent
✅ Remplacement des tableaux en mémoire par requêtes DB
✅ Gestion d'erreurs RFC 7807 maintenue
✅ Headers de corrélation propagés
✅ README mis à jour avec :
  - Instructions de configuration DB
  - Variables d'environnement requises
  - Commandes de migration
  - Tests d'intégration
✅ Au moins 3 commits Git aujourd'hui
✅ Code formaté et sans erreurs de lint

Équipe AUTH vérifie :

✅ Table users en base de données
✅ Hash bcrypt pour les mots de passe (work factor 12+)
✅ Génération de vrais JWT signés avec clé secrète
✅ Vérification de tokens (middleware d'authentification)
✅ Tests avec utilisateurs réels en DB
✅ Middleware d'authentification exportable pour les autres équipes
✅ Endpoints register/login fonctionnels
✅ Gestion des refresh tokens
✅ Tests d'authentification complets
✅ Documentation du middleware pour les autres équipes
✅ Au moins 3 commits Git aujourd'hui

Équipe FRONT vérifie :

✅ Configuration pour basculer mocks ↔ vrais services
✅ Formulaires de création (POST) fonctionnels
✅ Gestion des erreurs de validation côté UI
✅ Intégration avec Auth (login/register)
✅ Stockage du token (localStorage/sessionStorage)
✅ Affichage des données depuis la vraie DB
✅ Pagination côté UI fonctionnelle
✅ Filtres et recherche fonctionnels
✅ Gestion des états de chargement
✅ README avec instructions de configuration
✅ Au moins 3 commits Git aujourd'hui

📚 POUR DEMAIN (J4 Après-midi)
Théorie du matin (J4)

Sécurité API avancée : Rate limiting, CORS, headers de sécurité
RBAC (Role-Based Access Control) : Permissions granulaires
Audit et logging : Traçabilité des actions
Bonnes pratiques de sécurité en production

Pratique de l'après-midi (J4)
ÉQUIPES BACK :

Implémenter rate limiting (express-rate-limit, redis)
Configurer CORS correctement
Ajouter headers de sécurité (helmet)
Implémenter RBAC avec permissions granulaires
Ajouter audit logging (qui fait quoi, quand)
Tests de sécurité basiques

ÉQUIPE AUTH :

Implémenter refresh token rotation
Ajouter rate limiting sur les endpoints d'auth
Audit des tentatives de connexion
Gestion des sessions et déconnexion
Middleware RBAC avancé

ÉQUIPE FRONT :

Gestion des tokens expirés (refresh automatique)
Gestion des erreurs de sécurité
Interface d'administration (pour les admins)
Amélioration de l'UX de sécurité

À préparer ce soir (optionnel mais recommandé)
Pour toutes les équipes BACK :

Lire sur les stratégies de rate limiting
Comprendre CORS et les headers de sécurité
Réfléchir aux permissions nécessaires pour votre service

Pour l'équipe AUTH :

Lire sur les stratégies de refresh token
Comprendre les attaques par force brute
Réfléchir aux logs d'audit nécessaires

Pour tous :

Lire sur les bonnes pratiques de sécurité API
Comprendre les headers de sécurité (CSP, HSTS, etc.)

📈 PROGRESSION GLOBALE
J1 PM ✅ : Contrat OpenAPI + Structure projet
J2 PM ✅ : Endpoints mock + Tests + Prism
J3 PM ✅ : Base de données + CRUD complet + JWT réels
J4 PM 🎯 : Sécurité avancée + RBAC + Audit
J5 PM    : Intégration externe + Résilience + Communication
J6 PM    : API Gateway + Architecture distribuée
J7 PM    : Performance + Tests de charge + Observabilité
J8 PM    : Containerisation + CI/CD + Finalisation

Excellent travail aujourd'hui ! Vous avez maintenant des APIs complètement fonctionnelles avec base de données et authentification réelle. 🚀

💡 RETOUR D'EXPÉRIENCE DE L'ENSEIGNANT
Ce qui a bien marché aujourd'hui
✅ Les équipes qui ont préparé leur configuration DB en avance
✅ L'utilisation d'ORMs/ODMs pour simplifier les requêtes
✅ Les tests d'intégration écrits en parallèle du code
✅ La coordination Auth ↔ autres services sur le middleware

Points d'attention pour la suite
⚠️ Ne pas sous-estimer J4 : La sécurité prend du temps, prévoyez des marges
⚠️ Commiter souvent : 4-5 commits minimum par demi-journée
⚠️ Documenter les migrations : Très important pour la production
⚠️ Tester avec des données réalistes : Au moins 50-100 items pour tester les performances

Conseils pour J4

Commencez par les headers de sécurité (helmet), c'est le plus simple
Rate limiting : commencez par des valeurs conservatrices
RBAC : commencez simple (user/admin), vous pourrez complexifier
Audit : loggez au minimum les actions sensibles (création, modification, suppression)
