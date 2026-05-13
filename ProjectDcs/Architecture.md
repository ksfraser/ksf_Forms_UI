# Architecture - ksf_Forms_UI

## Document Information
- **Module**: ksf_Forms_UI
- **Version**: 1.0.0
- **Date**: 2026-05-13
- **Status**: Implemented
- **Author**: KSFII Development Team

---

## 1. Technical Architecture

### 1.1 Architecture Pattern
**Adapter Pattern** - ksf_Forms_UI acts as an adapter between the ksf_Forms business logic and the FrontAccounting platform UI.

```
┌─────────────────────────────────────────────────────────────┐
│                    FrontAccounting                          │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              ksf_Forms_UI (Adapter)                  │   │
│  │  ┌────────────────────────────────────────────────┐ │   │
│  │  │ - FA Menu Integration                          │ │   │
│  │  │ - Shortcode Processing                        │ │   │
│  │  │ - Form Rendering                              │ │   │
│  │  └────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                 │
│                           ▼                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              ksf_Forms (Business Logic)              │   │
│  │  ┌────────────────────────────────────────────────┐ │   │
│  │  │ - Form Entity                                 │ │   │
│  │  │ - FormField Entity                            │ │   │
│  │  │ - FormSubmission Entity                       │ │   │
│  │  │ - FormService                                 │ │   │
│  │  └────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Component Overview

| Component | Responsibility |
|-----------|---------------|
| Menu Registration | Register forms in FA menu system |
| Shortcode Handler | Process `[ksf_form]` shortcodes |
| Form Renderer | Render forms with FA styling |
| Submission Handler | Handle form POST submissions |

---

## 2. Data Flow Diagram

### 2.1 Form Rendering Flow

```
┌────────────┐     ┌──────────────┐     ┌────────────┐     ┌─────────────┐
│   User     │     │   FA Core    │     │ Forms_UI   │     │ Forms       │
│   Request  │     │              │     │            │     │             │
└────────────┘     └──────────────┘     └────────────┘     └─────────────┘
       │                 │                 │                 │
       │ GET /forms      │                 │                 │
       │────────────────>│                 │                 │
       │                 │                 │                 │
       │                 │ Render menu      │                 │
       │                 │─────────────────│                 │
       │                 │                 │                 │
       │                 │                 │ Load ksf_Forms  │
       │                 │                 │────────────────>│
       │                 │                 │                 │
       │                 │                 │<────────────────│
       │                 │                 │ Form data       │
       │                 │                 │                 │
       │                 │ Render FA view  │                 │
       │<────────────────│                 │                 │
       │ HTML response   │                 │                 │
```

### 2.2 Submission Handling Flow

```
┌────────────┐     ┌──────────────┐     ┌────────────┐     ┌─────────────┐
│   User     │     │   FA Core    │     │ Forms_UI   │     │ Forms       │
└────────────┘     └──────────────┘     └────────────┘     └─────────────┘
       │                 │                 │                 │
       │ POST form       │                 │                 │
       │────────────────>│                 │                 │
       │                 │                 │                 │
       │                 │ POST data       │                 │
       │                 │────────────────>│                 │
       │                 │                 │                 │
       │                 │                 │ processSubmission()
       │                 │                 │────────────────>│
       │                 │                 │                 │
       │                 │                 │<────────────────│
       │                 │                 │ Submission      │
       │                 │                 │                 │
       │                 │ Redirect/Result │                 │
       │<────────────────│                 │                 │
```

---

## 3. Integration Points

### 3.1 FA Hooks

| Hook | Purpose |
|------|---------|
| add_menu_entry | Register forms menu |
| add_shortcode | Register shortcode handler |
| form_validate | Custom validation |

### 3.2 ksf_Forms Integration

| Method | Usage |
|--------|-------|
| FormService::getForm() | Fetch form definition |
| FormService::processSubmission() | Handle submissions |
| FormService::generateCf7Form() | Generate form HTML |

---

## 4. File Structure

```
ksf_Forms_UI/
├── ProjectDcs/
│   ├── Business Requirements.md
│   ├── Architecture.md
│   ├── Functional Requirements.md
│   ├── Use Case.md
│   ├── Test Plan.md
│   └── UAT Plan.md
└── src/
    ├── (UI adapter classes if needed)
```

---

## 5. Configuration

### 5.1 Menu Configuration

```php
// Register forms menu
add_menu_entry(
    'forms',
    'Forms',
    'forms',
    'ksf_forms'
);
```

### 5.2 Shortcode Configuration

```php
// Register shortcode
add_shortcode(
    'ksf_form',
    'ksf_forms_render_shortcode'
);
```

---

## 6. Error Handling

| Scenario | Handling |
|----------|----------|
| Form not found | Display 404 message |
| Submission error | Log and show error |
| Invalid CSRF | Reject submission |

---

## 7. Security

- CSRF tokens validated via FA framework
- Input sanitization via ksf_Forms
- Permission checks via FA auth system

---

*Document Version: 1.0.0*
*Last Updated: 2026-05-13*