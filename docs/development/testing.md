# 🧪 Testing Guide

Comprehensive testing strategy and guidelines for **Jura Modular**.

## 🎯 Testing Philosophy

> **Quality First**: Every feature must be thoroughly tested before integration.

### Testing Pyramid

```
    🔺 E2E Tests (5%)
      - Full user workflows
      - Browser automation
      - API integration tests

   🔺 Integration Tests (15%)
     - Database interactions
     - API endpoint testing
     - Service integration

  🔺 Unit Tests (80%)
    - Individual functions
    - Model validation
    - Business logic
```

### Test-Driven Development (TDD)

1. **🔴 Red**: Write failing test first
2. **🟢 Green**: Write minimal code to pass
3. **🔵 Refactor**: Improve code while keeping tests green

## 🛠️ Testing Setup

### Framework Stack

| Tool | Purpose | Documentation |
|------|---------|---------------|
| **pytest** | Test runner | [pytest.org](https://pytest.org) |
| **pytest-django** | Django integration | [pytest-django.readthedocs.io](https://pytest-django.readthedocs.io) |
| **factory-boy** | Test data generation | [factoryboy.readthedocs.io](https://factoryboy.readthedocs.io) |
| **pytest-cov** | Coverage reporting | [pytest-cov.readthedocs.io](https://pytest-cov.readthedocs.io) |
| **pytest-mock** | Mocking utilities | [pytest-mock.readthedocs.io](https://pytest-mock.readthedocs.io) |

### Configuration Files

#### `pytest.ini`

```ini
[tool:pytest]
DJANGO_SETTINGS_MODULE = kanzlei_backend.settings_test
python_files = tests.py test_*.py *_tests.py
python_classes = Test* *Tests
python_functions = test_*
addopts = 
    --verbose
    --tb=short
    --strict-markers
    --disable-warnings
    --reuse-db
    --nomigrations
    --cov=kanzlei_apps
    --cov-report=html:htmlcov
    --cov-report=term-missing:skip-covered
    --cov-fail-under=85
testpaths = tests
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    unit: unit tests
    integration: integration tests  
    e2e: end-to-end tests
    external: tests requiring external services
    security: security-related tests
```

#### Test Settings (`kanzlei_backend/settings_test.py`)

```python
from .settings import *

# Test Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': ':memory:',
    }
}

# Disable migrations for faster tests
class DisableMigrations:
    def __contains__(self, item):
        return True
    
    def __getitem__(self, item):
        return None

MIGRATION_MODULES = DisableMigrations()

# Fast password hashing
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.MD5PasswordHasher',
]

# Disable logging during tests
LOGGING_CONFIG = None

# Email backend for testing
EMAIL_BACKEND = 'django.core.mail.backends.locmem.EmailBackend'

# Disable caching
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
    }
}

# Security settings for tests
SECRET_KEY = 'test-secret-key-not-for-production'
DEBUG = False
ALLOWED_HOSTS = ['testserver']

# Celery settings for tests
CELERY_TASK_ALWAYS_EAGER = True
CELERY_TASK_EAGER_PROPAGATES = True
```

## 📁 Test Organization

### Directory Structure

```
tests/
├── __init__.py
├── conftest.py                  # pytest configuration
├── factories.py                 # Factory Boy factories
├── utils.py                     # Test utilities
├── fixtures/                    # Test data files
│   ├── sample_documents.json
│   └── test_users.json
├── accounts/                    # Accounts app tests
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_views.py
│   ├── test_serializers.py
│   ├── test_permissions.py
│   └── test_admin.py
├── cases/                       # Cases app tests
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_views.py
│   └── test_workflows.py
├── appointments/                # Appointments app tests
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_views.py
│   └── test_scheduling.py
├── integration/                 # Integration tests
│   ├── __init__.py
│   ├── test_api_workflows.py
│   ├── test_database_operations.py
│   └── test_email_notifications.py
└── e2e/                        # End-to-end tests
    ├── __init__.py
    ├── test_user_registration.py
    ├── test_case_management.py
    └── test_appointment_booking.py
```

### Test Naming Conventions

```python
# Model Tests
def test_user_creation_with_valid_data():
def test_user_str_representation():
def test_user_role_validation():

# View Tests  
def test_login_view_success():
def test_login_view_invalid_credentials():
def test_profile_view_requires_authentication():

# API Tests
def test_api_user_list_returns_correct_data():
def test_api_user_create_validates_required_fields():
def test_api_user_update_permission_denied():

# Integration Tests
def test_complete_user_registration_workflow():
def test_case_creation_with_client_assignment():
```

## 🏭 Test Factories

### Base Factory Setup

```python
# tests/factories.py
import factory
import factory.fuzzy
from django.contrib.auth import get_user_model
from django.utils import timezone
from kanzlei_apps.accounts.models import ClientProfile, TwoFactorSettings
from kanzlei_apps.cases.models import Case, CaseType

User = get_user_model()

class UserFactory(factory.django.DjangoModelFactory):
    """Factory for creating test users."""
    
    class Meta:
        model = User
    
    username = factory.Sequence(lambda n: f"user{n}")
    email = factory.LazyAttribute(lambda obj: f"{obj.username}@example.com")
    first_name = factory.Faker("first_name")
    last_name = factory.Faker("last_name")
    phone_number = factory.Faker("phone_number")
    role = "client"
    is_active = True
    two_factor_enabled = False
    
    @factory.post_generation
    def password(obj, create, extracted, **kwargs):
        if extracted:
            obj.set_password(extracted)
        else:
            obj.set_password('testpass123')
        if create:
            obj.save()

class LawyerFactory(UserFactory):
    """Factory for lawyer users."""
    role = "lawyer"
    
class AdminUserFactory(UserFactory):
    """Factory for admin users."""
    role = "admin"
    is_staff = True
    is_superuser = True

class ClientProfileFactory(factory.django.DjangoModelFactory):
    """Factory for client profiles."""
    
    class Meta:
        model = ClientProfile
    
    user = factory.SubFactory(UserFactory, role="client")
    user_type = "private"
    phone_number = factory.Faker("phone_number")
    address = factory.Faker("address")
    city = factory.Faker("city")
    postal_code = factory.Faker("postcode")
    country = "DE"
    date_of_birth = factory.Faker("date_of_birth", minimum_age=18, maximum_age=90)
    
class CompanyClientProfileFactory(ClientProfileFactory):
    """Factory for company client profiles."""
    user_type = "company"
    company_name = factory.Faker("company")
    tax_number = factory.Faker("ssn")
    
class TwoFactorSettingsFactory(factory.django.DjangoModelFactory):
    """Factory for 2FA settings."""
    
    class Meta:
        model = TwoFactorSettings
    
    user = factory.SubFactory(UserFactory, two_factor_enabled=True)
    backup_tokens = factory.LazyFunction(
        lambda: [f"backup{i:04d}" for i in range(10)]
    )
    recovery_email = factory.LazyAttribute(
        lambda obj: f"recovery.{obj.user.username}@example.com"
    )

class CaseTypeFactory(factory.django.DjangoModelFactory):
    """Factory for case types."""
    
    class Meta:
        model = CaseType
        django_get_or_create = ('name',)
    
    name = factory.Iterator([
        "Scheidung", "Verkehrsrecht", "Arbeitsrecht", 
        "Immobilienrecht", "Strafrecht"
    ])
    description = factory.Faker("text", max_nb_chars=200)
    hourly_rate = factory.fuzzy.FuzzyDecimal(150.00, 500.00, 2)

class CaseFactory(factory.django.DjangoModelFactory):
    """Factory for legal cases."""
    
    class Meta:
        model = Case
    
    title = factory.Faker("sentence", nb_words=4)
    description = factory.Faker("text")
    case_number = factory.Sequence(lambda n: f"CASE-{n:06d}")
    client = factory.SubFactory(UserFactory, role="client")
    lawyer = factory.SubFactory(UserFactory, role="lawyer")
    case_type = factory.SubFactory(CaseTypeFactory)
    status = "open"
    priority = "medium"
    created_at = factory.LazyFunction(timezone.now)
    budget = factory.fuzzy.FuzzyDecimal(1000.00, 50000.00, 2)
```

### Advanced Factory Patterns

```python
# Custom factory methods
class UserFactory(factory.django.DjangoModelFactory):
    # ... base factory ...
    
    @classmethod
    def create_with_profile(cls, **kwargs):
        """Create user with associated client profile."""
        user = cls.create(role="client", **kwargs)
        ClientProfileFactory.create(user=user)
        return user
    
    @classmethod
    def create_with_2fa(cls, **kwargs):
        """Create user with 2FA enabled."""
        user = cls.create(two_factor_enabled=True, **kwargs)
        TwoFactorSettingsFactory.create(user=user)
        return user

# Trait-based factories
class CaseFactory(factory.django.DjangoModelFactory):
    # ... base factory ...
    
    class Params:
        urgent = factory.Trait(
            priority="high",
            status="in_progress"
        )
        closed = factory.Trait(
            status="closed",
            closed_at=factory.LazyFunction(timezone.now)
        )

# Usage:
urgent_case = CaseFactory(urgent=True)
closed_case = CaseFactory(closed=True)
```

## 🧪 Unit Testing

### Model Testing

```python
# tests/accounts/test_models.py
import pytest
from django.core.exceptions import ValidationError
from django.contrib.auth import get_user_model
from tests.factories import UserFactory, ClientProfileFactory

User = get_user_model()

class TestUserModel:
    """Test cases for the User model."""
    
    def test_user_creation_with_valid_data(self):
        """Test creating user with valid data."""
        user = UserFactory.build()
        user.full_clean()  # Triggers model validation
        user.save()
        
        assert User.objects.filter(username=user.username).exists()
        assert user.get_full_name() == f"{user.first_name} {user.last_name}"
    
    def test_user_str_representation(self):
        """Test user string representation."""
        user = UserFactory(first_name="Max", last_name="Mustermann")
        assert str(user) == "Max Mustermann"
    
    def test_user_role_validation(self):
        """Test user role field validation."""
        valid_roles = ["admin", "lawyer", "assistant", "client", "property_manager"]
        
        for role in valid_roles:
            user = UserFactory.build(role=role)
            user.full_clean()  # Should not raise
    
    def test_user_role_invalid_choice(self):
        """Test invalid role raises validation error."""
        with pytest.raises(ValidationError):
            user = UserFactory.build(role="invalid_role")
            user.full_clean()
    
    def test_user_email_uniqueness(self):
        """Test that user emails must be unique."""
        UserFactory(email="test@example.com")
        
        with pytest.raises(ValidationError):
            duplicate_user = UserFactory.build(email="test@example.com")
            duplicate_user.full_clean()
    
    def test_user_is_lawyer_property(self):
        """Test is_lawyer property."""
        lawyer = UserFactory(role="lawyer")
        client = UserFactory(role="client")
        
        assert lawyer.is_lawyer is True
        assert client.is_lawyer is False
    
    def test_user_can_manage_cases_permission(self):
        """Test can_manage_cases method."""
        lawyer = UserFactory(role="lawyer")
        admin = UserFactory(role="admin")
        client = UserFactory(role="client")
        
        assert lawyer.can_manage_cases() is True
        assert admin.can_manage_cases() is True
        assert client.can_manage_cases() is False

class TestClientProfileModel:
    """Test cases for ClientProfile model."""
    
    def test_client_profile_creation(self):
        """Test creating client profile."""
        profile = ClientProfileFactory()
        
        assert profile.user.role == "client"
        assert profile.user_type in ["private", "company"]
    
    def test_company_profile_requires_company_name(self):
        """Test company profiles require company name."""
        profile = ClientProfileFactory.build(
            user_type="company",
            company_name=""
        )
        
        with pytest.raises(ValidationError):
            profile.full_clean()
    
    def test_private_profile_company_name_optional(self):
        """Test private profiles don't require company name."""
        profile = ClientProfileFactory.build(
            user_type="private",
            company_name=""
        )
        
        profile.full_clean()  # Should not raise
    
    @pytest.mark.parametrize("user_type,expected", [
        ("private", "Private Client"),
        ("company", "Company Client"),
    ])
    def test_profile_type_display(self, user_type, expected):
        """Test profile type display method."""
        profile = ClientProfileFactory(user_type=user_type)
        assert profile.get_user_type_display() == expected
```

### View Testing

```python
# tests/accounts/test_views.py
import pytest
from django.urls import reverse
from django.contrib.auth import get_user_model
from rest_framework.test import APIClient
from rest_framework import status
from tests.factories import UserFactory, LawyerFactory

User = get_user_model()

@pytest.mark.django_db
class TestUserRegistrationView:
    """Test user registration API."""
    
    def setup_method(self):
        """Set up test client."""
        self.client = APIClient()
        self.url = reverse('api:accounts:register')
    
    def test_register_user_success(self):
        """Test successful user registration."""
        data = {
            'username': 'newuser',
            'email': 'newuser@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'first_name': 'New',
            'last_name': 'User',
            'phone_number': '+49123456789',
            'role': 'client'
        }
        
        response = self.client.post(self.url, data)
        
        assert response.status_code == status.HTTP_201_CREATED
        assert User.objects.filter(username='newuser').exists()
        assert 'access_token' in response.data
        assert 'refresh_token' in response.data
    
    def test_register_user_password_mismatch(self):
        """Test registration with password mismatch."""
        data = {
            'username': 'newuser',
            'email': 'newuser@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'DifferentPass123!',
            'first_name': 'New',
            'last_name': 'User',
            'role': 'client'
        }
        
        response = self.client.post(self.url, data)
        
        assert response.status_code == status.HTTP_400_BAD_REQUEST
        assert 'password' in response.data
    
    def test_register_user_weak_password(self):
        """Test registration with weak password."""
        data = {
            'username': 'newuser',
            'email': 'newuser@example.com',
            'password': '123',
            'password_confirm': '123',
            'first_name': 'New',
            'last_name': 'User',
            'role': 'client'
        }
        
        response = self.client.post(self.url, data)
        
        assert response.status_code == status.HTTP_400_BAD_REQUEST
        assert 'password' in response.data
    
    def test_register_duplicate_username(self):
        """Test registration with existing username."""
        UserFactory(username='existinguser')
        
        data = {
            'username': 'existinguser',
            'email': 'newemail@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'role': 'client'
        }
        
        response = self.client.post(self.url, data)
        
        assert response.status_code == status.HTTP_400_BAD_REQUEST
        assert 'username' in response.data

@pytest.mark.django_db
class TestUserProfileView:
    """Test user profile API."""
    
    def setup_method(self):
        """Set up authenticated client."""
        self.user = UserFactory()
        self.client = APIClient()
        self.client.force_authenticate(user=self.user)
        self.url = reverse('api:accounts:profile')
    
    def test_get_profile_authenticated(self):
        """Test getting profile for authenticated user."""
        response = self.client.get(self.url)
        
        assert response.status_code == status.HTTP_200_OK
        assert response.data['username'] == self.user.username
        assert response.data['email'] == self.user.email
    
    def test_get_profile_unauthenticated(self):
        """Test getting profile without authentication."""
        self.client.force_authenticate(user=None)
        response = self.client.get(self.url)
        
        assert response.status_code == status.HTTP_401_UNAUTHORIZED
    
    def test_update_profile_success(self):
        """Test updating user profile."""
        data = {
            'first_name': 'Updated',
            'last_name': 'Name',
            'phone_number': '+49987654321'
        }
        
        response = self.client.patch(self.url, data)
        
        assert response.status_code == status.HTTP_200_OK
        self.user.refresh_from_db()
        assert self.user.first_name == 'Updated'
        assert self.user.last_name == 'Name'
    
    def test_update_profile_readonly_fields(self):
        """Test that readonly fields cannot be updated."""
        original_username = self.user.username
        
        data = {
            'username': 'newusername',
            'email': 'newemail@example.com',
            'role': 'admin'
        }
        
        response = self.client.patch(self.url, data)
        
        self.user.refresh_from_db()
        assert self.user.username == original_username
        # Email and role should not change for regular users
```

### Serializer Testing

```python
# tests/accounts/test_serializers.py
import pytest
from django.contrib.auth import get_user_model
from kanzlei_apps.accounts.serializers import (
    UserRegistrationSerializer,
    UserProfileSerializer,
    TwoFactorSetupSerializer
)
from tests.factories import UserFactory

User = get_user_model()

class TestUserRegistrationSerializer:
    """Test user registration serializer."""
    
    def test_valid_data_serialization(self):
        """Test serializer with valid data."""
        data = {
            'username': 'testuser',
            'email': 'test@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'first_name': 'Test',
            'last_name': 'User',
            'role': 'client'
        }
        
        serializer = UserRegistrationSerializer(data=data)
        assert serializer.is_valid()
    
    def test_password_mismatch_validation(self):
        """Test password confirmation validation."""
        data = {
            'username': 'testuser',
            'email': 'test@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'DifferentPass123!',
            'first_name': 'Test',
            'last_name': 'User',
            'role': 'client'
        }
        
        serializer = UserRegistrationSerializer(data=data)
        assert not serializer.is_valid()
        assert 'password' in serializer.errors
    
    def test_user_creation(self):
        """Test actual user creation through serializer."""
        data = {
            'username': 'testuser',
            'email': 'test@example.com', 
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'first_name': 'Test',
            'last_name': 'User',
            'role': 'client'
        }
        
        serializer = UserRegistrationSerializer(data=data)
        assert serializer.is_valid()
        
        user = serializer.save()
        assert user.username == 'testuser'
        assert user.check_password('StrongPass123!')
        assert user.role == 'client'

class TestUserProfileSerializer:
    """Test user profile serializer."""
    
    def test_serialization(self):
        """Test serializing user data."""
        user = UserFactory()
        serializer = UserProfileSerializer(user)
        
        data = serializer.data
        assert data['username'] == user.username
        assert data['email'] == user.email
        assert data['role'] == user.role
    
    def test_partial_update(self):
        """Test partial profile update."""
        user = UserFactory()
        data = {'first_name': 'Updated'}
        
        serializer = UserProfileSerializer(user, data=data, partial=True)
        assert serializer.is_valid()
        
        updated_user = serializer.save()
        assert updated_user.first_name == 'Updated'
    
    def test_readonly_fields(self):
        """Test that certain fields are readonly."""
        user = UserFactory()
        original_username = user.username
        
        data = {
            'username': 'newusername',
            'role': 'admin',
            'is_superuser': True
        }
        
        serializer = UserProfileSerializer(user, data=data, partial=True)
        # Should be valid because readonly fields are ignored
        assert serializer.is_valid()
        
        updated_user = serializer.save()
        assert updated_user.username == original_username
        assert updated_user.role != 'admin'
```

## 🔗 Integration Testing

### API Workflow Testing

```python
# tests/integration/test_api_workflows.py
import pytest
from django.urls import reverse
from rest_framework.test import APIClient
from rest_framework import status
from tests.factories import UserFactory, CaseFactory

@pytest.mark.django_db
class TestCompleteUserWorkflow:
    """Test complete user workflows."""
    
    def setup_method(self):
        """Set up test environment."""
        self.client = APIClient()
    
    def test_complete_registration_and_profile_workflow(self):
        """Test complete user registration and profile setup."""
        # 1. Register new user
        registration_data = {
            'username': 'newclient',
            'email': 'newclient@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'first_name': 'New',
            'last_name': 'Client',
            'role': 'client'
        }
        
        register_url = reverse('api:accounts:register')
        response = self.client.post(register_url, registration_data)
        
        assert response.status_code == status.HTTP_201_CREATED
        tokens = response.data
        
        # 2. Use access token to authenticate
        self.client.credentials(
            HTTP_AUTHORIZATION=f'Bearer {tokens["access_token"]}'
        )
        
        # 3. Get user profile
        profile_url = reverse('api:accounts:profile')
        response = self.client.get(profile_url)
        
        assert response.status_code == status.HTTP_200_OK
        assert response.data['username'] == 'newclient'
        
        # 4. Update profile
        update_data = {
            'phone_number': '+49123456789',
            'first_name': 'Updated'
        }
        
        response = self.client.patch(profile_url, update_data)
        
        assert response.status_code == status.HTTP_200_OK
        assert response.data['first_name'] == 'Updated'
        
        # 5. Setup 2FA
        setup_2fa_url = reverse('api:accounts:2fa-setup')
        response = self.client.post(setup_2fa_url)
        
        assert response.status_code == status.HTTP_200_OK
        assert 'qr_code' in response.data
        assert 'backup_tokens' in response.data

@pytest.mark.django_db
class TestCaseManagementWorkflow:
    """Test case management workflows."""
    
    def setup_method(self):
        """Set up authenticated users."""
        self.lawyer = UserFactory(role='lawyer')
        self.client_user = UserFactory(role='client')
        self.client = APIClient()
    
    def test_lawyer_creates_case_for_client(self):
        """Test lawyer creating a case for a client."""
        # Authenticate as lawyer
        self.client.force_authenticate(user=self.lawyer)
        
        # Create case
        case_data = {
            'title': 'New Legal Case',
            'description': 'Case description',
            'client': self.client_user.id,
            'case_type': 'contract',
            'priority': 'medium'
        }
        
        create_url = reverse('api:cases:case-list')
        response = self.client.post(create_url, case_data)
        
        assert response.status_code == status.HTTP_201_CREATED
        case_id = response.data['id']
        
        # Verify case was created
        detail_url = reverse('api:cases:case-detail', kwargs={'pk': case_id})
        response = self.client.get(detail_url)
        
        assert response.status_code == status.HTTP_200_OK
        assert response.data['title'] == 'New Legal Case'
        assert response.data['lawyer'] == self.lawyer.id
        assert response.data['client'] == self.client_user.id
        
        # Client should be able to view their case
        self.client.force_authenticate(user=self.client_user)
        response = self.client.get(detail_url)
        
        assert response.status_code == status.HTTP_200_OK
```

### Database Integration Tests

```python
# tests/integration/test_database_operations.py
import pytest
from django.db import transaction
from django.db.utils import IntegrityError
from tests.factories import UserFactory, CaseFactory, ClientProfileFactory

@pytest.mark.django_db
class TestDatabaseConstraints:
    """Test database constraints and relationships."""
    
    def test_user_email_uniqueness_constraint(self):
        """Test database enforces email uniqueness."""
        UserFactory(email='test@example.com')
        
        with pytest.raises(IntegrityError):
            UserFactory(email='test@example.com')
    
    def test_client_profile_user_relationship(self):
        """Test client profile foreign key relationship."""
        user = UserFactory(role='client')
        profile = ClientProfileFactory(user=user)
        
        # Test cascade delete
        user.delete()
        
        with pytest.raises(ClientProfile.DoesNotExist):
            profile.refresh_from_db()
    
    def test_case_assignments_relationships(self):
        """Test case assignment relationships."""
        lawyer = UserFactory(role='lawyer')
        client = UserFactory(role='client')
        case = CaseFactory(lawyer=lawyer, client=client)
        
        # Verify relationships
        assert case.lawyer == lawyer
        assert case.client == client
        assert case in lawyer.assigned_cases.all()
        assert case in client.client_cases.all()

@pytest.mark.django_db
class TestTransactionHandling:
    """Test transaction handling."""
    
    def test_atomic_user_creation_with_profile(self):
        """Test atomic transaction for user + profile creation."""
        
        def create_user_with_profile():
            with transaction.atomic():
                user = UserFactory(role='client')
                # Simulate error during profile creation
                if user.username == 'erroruser':
                    raise Exception("Simulated error")
                ClientProfileFactory(user=user)
                return user
        
        # Successful creation
        user = create_user_with_profile()
        assert user.client_profile is not None
        
        # Failed creation should rollback everything
        with pytest.raises(Exception):
            UserFactory(username='erroruser')
            create_user_with_profile()
        
        # Verify no user was created
        assert not User.objects.filter(username='erroruser').exists()
```

## 🎭 End-to-End Testing

### Browser Testing with Playwright

```python
# tests/e2e/test_user_registration.py
import pytest
from playwright.sync_api import Page, expect

@pytest.mark.e2e
class TestUserRegistrationE2E:
    """End-to-end tests for user registration flow."""
    
    def test_complete_registration_flow(self, page: Page):
        """Test complete user registration through UI."""
        # Navigate to registration page
        page.goto("http://localhost:4200/register")
        
        # Fill registration form
        page.fill('[data-testid="username"]', 'testuser')
        page.fill('[data-testid="email"]', 'test@example.com')
        page.fill('[data-testid="password"]', 'StrongPass123!')
        page.fill('[data-testid="password-confirm"]', 'StrongPass123!')
        page.fill('[data-testid="first-name"]', 'Test')
        page.fill('[data-testid="last-name"]', 'User')
        page.select_option('[data-testid="role"]', 'client')
        
        # Submit form
        page.click('[data-testid="register-button"]')
        
        # Verify successful registration
        expect(page).to_have_url("http://localhost:4200/dashboard")
        expect(page.locator('[data-testid="welcome-message"]')).to_contain_text("Welcome, Test User")
    
    def test_registration_validation_errors(self, page: Page):
        """Test registration form validation."""
        page.goto("http://localhost:4200/register")
        
        # Submit empty form
        page.click('[data-testid="register-button"]')
        
        # Check validation errors
        expect(page.locator('[data-testid="username-error"]')).to_be_visible()
        expect(page.locator('[data-testid="email-error"]')).to_be_visible()
        
        # Test password mismatch
        page.fill('[data-testid="password"]', 'password123')
        page.fill('[data-testid="password-confirm"]', 'different123')
        page.click('[data-testid="register-button"]')
        
        expect(page.locator('[data-testid="password-error"]')).to_contain_text("Passwords must match")

@pytest.mark.e2e
class TestCaseManagementE2E:
    """End-to-end tests for case management."""
    
    def test_lawyer_creates_case(self, page: Page, authenticated_lawyer):
        """Test lawyer creating a case through UI."""
        # Login is handled by authenticated_lawyer fixture
        
        # Navigate to case creation
        page.goto("http://localhost:4200/cases/new")
        
        # Fill case form
        page.fill('[data-testid="case-title"]', 'New Legal Case')
        page.fill('[data-testid="case-description"]', 'Case description here')
        page.select_option('[data-testid="case-type"]', 'contract')
        page.select_option('[data-testid="client"]', 'client@example.com')
        
        # Submit case
        page.click('[data-testid="create-case-button"]')
        
        # Verify case creation
        expect(page).to_have_url(re.compile(r"/cases/\d+"))
        expect(page.locator('[data-testid="case-title"]')).to_contain_text("New Legal Case")
```

### API Integration Testing

```python
# tests/e2e/test_api_integration.py
import pytest
import requests
from django.conf import settings

@pytest.mark.e2e
@pytest.mark.external
class TestAPIIntegration:
    """Test API integration with external services."""
    
    def setup_method(self):
        """Set up API client."""
        self.base_url = "http://localhost:8000/api"
        self.session = requests.Session()
    
    def test_complete_api_workflow(self):
        """Test complete API workflow."""
        # 1. Register user
        registration_data = {
            'username': 'apitest',
            'email': 'apitest@example.com',
            'password': 'StrongPass123!',
            'password_confirm': 'StrongPass123!',
            'role': 'client'
        }
        
        response = self.session.post(
            f"{self.base_url}/accounts/register/",
            json=registration_data
        )
        
        assert response.status_code == 201
        tokens = response.json()
        
        # 2. Set authentication headers
        self.session.headers.update({
            'Authorization': f'Bearer {tokens["access_token"]}'
        })
        
        # 3. Get user profile
        response = self.session.get(f"{self.base_url}/accounts/profile/")
        assert response.status_code == 200
        
        profile = response.json()
        assert profile['username'] == 'apitest'
        
        # 4. Update profile
        update_data = {'phone_number': '+49123456789'}
        response = self.session.patch(
            f"{self.base_url}/accounts/profile/",
            json=update_data
        )
        
        assert response.status_code == 200
        assert response.json()['phone_number'] == '+49123456789'
        
        # 5. Test token refresh
        refresh_data = {'refresh': tokens['refresh_token']}
        response = self.session.post(
            f"{self.base_url}/auth/token/refresh/",
            json=refresh_data
        )
        
        assert response.status_code == 200
        new_access_token = response.json()['access']
        
        # 6. Use new token
        self.session.headers.update({
            'Authorization': f'Bearer {new_access_token}'
        })
        
        response = self.session.get(f"{self.base_url}/accounts/profile/")
        assert response.status_code == 200
```

## 📊 Test Coverage & Reporting

### Coverage Configuration

```python
# .coveragerc
[run]
source = kanzlei_apps
omit = 
    */migrations/*
    */tests/*
    */venv/*
    */settings/*
    manage.py
    */conftest.py
    */factory.py

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise AssertionError
    raise NotImplementedError
    if __name__ == .__main__.:
    class Meta:

[html]
directory = htmlcov
```

### Running Tests & Coverage

```bash
# Run all tests
pytest

# Run specific test module
pytest tests/accounts/

# Run with coverage
pytest --cov=kanzlei_apps --cov-report=html

# Run tests by marker
pytest -m unit                    # Only unit tests
pytest -m "not slow"             # Exclude slow tests
pytest -m "unit and not external" # Unit tests, no external deps

# Run failed tests from last run
pytest --lf

# Run tests in parallel
pytest -n auto

# Generate coverage report
coverage html
coverage report --show-missing
```

### Test Reporting

```python
# conftest.py additions for reporting
def pytest_configure(config):
    """Configure pytest with custom markers."""
    config.addinivalue_line("markers", "unit: Unit tests")
    config.addinivalue_line("markers", "integration: Integration tests")
    config.addinivalue_line("markers", "e2e: End-to-end tests")
    config.addinivalue_line("markers", "slow: Slow running tests")
    config.addinivalue_line("markers", "external: Tests requiring external services")

def pytest_html_report_title(report):
    """Custom HTML report title."""
    report.title = "Jura Modular Test Report"

@pytest.fixture(scope="session")
def django_db_setup():
    """Custom database setup for testing."""
    from django.core.management import call_command
    call_command("migrate", verbosity=0, interactive=False)
```

## 🚀 Test Automation

### GitHub Actions Workflow

```yaml
# .github/workflows/tests.yml
name: Test Suite

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: test_jura_modular
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      
      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    strategy:
      matrix:
        python-version: [3.12]
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r backend/requirements.txt
        pip install -r backend/requirements-dev.txt
    
    - name: Run linting
      run: |
        flake8 backend/kanzlei_apps/
        black --check backend/
        isort --check backend/
    
    - name: Run tests
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost/test_jura_modular
        REDIS_URL: redis://localhost:6379/0
      run: |
        cd backend
        pytest --cov=kanzlei_apps --cov-report=xml
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./backend/coverage.xml
        fail_ci_if_error: true
```

### Pre-commit Quality Gates

```bash
# Install quality gates
pip install pre-commit
pre-commit install

# Run quality checks
pre-commit run --all-files

# Skip hooks for emergency commits
git commit -m "emergency fix" --no-verify
```

---

Mit dieser umfassenden Testing-Strategie gewährleisten wir höchste Code-Qualität für **Jura Modular**! 🧪✅
