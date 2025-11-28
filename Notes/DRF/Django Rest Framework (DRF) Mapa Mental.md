
# Django Rest Framework (DRF) Mapa Mental
## 1. Introdução
- **O que é DRF?**
  - Ferramenta poderosa e flexível para construir APIs Web
- **Por que usar DRF?**
  - Simplicidade, Flexibilidade, Extensibilidade

## 2. Instalação
- **Pré-requisitos**
  - Python, Django
- **Passos de instalação**
  - Instalar via pip: `pip install djangorestframework`
  - Adicionar `'rest_framework'` a `INSTALLED_APPS` no `settings.py`

## 3. Configuração
- **Configuração básica**
  - Adicionar configurações do DRF no `settings.py`
  - Exemplo:      
	```python
	  REST_FRAMEWORK = { 
		'DEFAULT_AUTHENTICATION_CLASSES': [
			'rest_framework.authentication.SessionAuthentication',
			'rest_framework.authentication.BasicAuthentication'
		],
		'DEFAULT_PERMISSION_CLASSES': [
			'rest_framework.permissions.AllowAny',
		],
	}      
	```

## 4. Serializers
- **O que são Serializers?**
  - Transformam dados complexos em tipos de dados nativos do Python
- **Tipos de Serializers**
  - `ModelSerializer`
  - `Serializer`
- **Campos**
  - Tipos de campos: `CharField`, `IntegerField`, `EmailField`, etc.
  - Campos personalizados

## 5. Views
- **ViewSets**
  - `ModelViewSet`, `ReadOnlyModelViewSet`
- **APIView**
  - Métodos HTTP: `get`, `post`, `put`, `delete`
- **Mixins**
  - `CreateModelMixin`, `RetrieveModelMixin`, `UpdateModelMixin`, `DestroyModelMixin`

## 6. URLs e roteamento
- **Roteamento padrão**
  - `DefaultRouter`, `SimpleRouter`
- **Registro de ViewSets**
  - Exemplo:
  
```python
    from rest_framework.routers import DefaultRouter
    from myapp.views import MyModelViewSet

    router = DefaultRouter()
    router.register(r'mymodels', MyModelViewSet)

    urlpatterns = router.urls  
```

## 7. Autenticação e Permissões
- **Classes de Autenticação**
  - `BasicAuthentication`, `SessionAuthentication`, `TokenAuthentication`
- **Classes de Permissão**
  - `AllowAny`, `IsAuthenticated`, `IsAdminUser`, `IsAuthenticatedOrReadOnly`

## 8. Throttling
- **Visão Geral**
  - Limitação de taxa para prevenir abusos
- **Classes de Throttling**
  - `UserRateThrottle`, `AnonRateThrottle`
- **Configuração**
  - Configuração no `settings.py`

## 9. Paginação
- **Tipos de Paginação**
  - `PageNumberPagination`, `LimitOffsetPagination`, `CursorPagination`
- **Configuração**
  - Configuração no `settings.py`

## 10. Filtros e Pesquisas
- **DjangoFilterBackend**
  - Integração com django-filter
- **SearchFilter**
  - Pesquisa de texto simples
- **OrderingFilter**
  - Ordenação de resultados

## 11. Testes
- **APITestCase**
  - Herda de `TestCase`
- **APIClient**
  - Cliente de teste para fazer requests a API

## 12. Performance e Caching
- **Dicas de Performance**
  - Usar `select_related` e `prefetch_related`
- **Caching**
  - Usar caching de consultas com `cache_response`

## 13. Internacionalização
- **Suporte a i18n**
  - Tradução de mensagens

## 14. Extensibilidade
- **Customizações**
  - Serializers, Views, Permissions, Throttling, etc.
- **Signal Handlers**
  - Conectar funções a sinais de Django

## 15. Deployment
- **Considerações de Produção**
  - Configuração de servidores, segurança, performance
- **Ferramentas**
  - Docker, CI/CD, etc.


