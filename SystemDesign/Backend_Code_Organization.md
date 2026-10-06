# Backend Code Organization

Where responsibilities live in `vip-web-backend`. Used on the **"Backend Organization"** presentation slide.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true, 'wrappingWidth': 260}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TB
    subgraph project ["Django project · backend/"]
        settings["<b>settings.py</b><br/>env config, DRF"]
        urls["<b>urls.py</b><br/>root routes"]
        hello["<b>views.py</b><br/>hello"]
        health["<b>health.py</b><br/>/healthz/"]
        server["<b>asgi.py · wsgi.py</b><br/>server entry points"]
    end

    subgraph domain ["Events domain · events/"]
        eurls["<b>urls.py</b><br/>events routes"]
        eviews["<b>views.py</b><br/>list · create · detail"]
        ser["<b>serializers.py</b><br/>validation"]
        model["<b>models.py</b><br/>Event"]
        mig["<b>migrations/</b><br/>schema history"]
        exc["<b>exceptions.py</b><br/>error envelope"]
    end

    subgraph verify ["Verification · tests/"]
        tapi["<b>test_events_api.py</b><br/><b>test_events_fields.py</b>"]
        helpers["<b>events_helpers.py</b><br/>shared helpers"]
        tops["<b>test_healthz.py</b><br/><b>test_hello.py</b>"]
    end

    subgraph tooling ["Runtime & tooling"]
        manage["manage.py"]
        docker["Dockerfile<br/>docker-compose.yml"]
        cfg["requirements.txt<br/>pyproject.toml<br/>.env.example"]
        ci[".github/workflows/ci.yml"]
    end

    urls -->|"api/v1/staff/"| eurls
    urls --> health
    urls --> hello
    eurls --> eviews
    eviews --> ser
    ser -->|"validate + save"| model
    settings -.->|"registers"| exc
    eurls -.-|"verified by"| tapi
    health -.-|"verified by"| tops
    verify -.-|"run in CI"| ci

    classDef code fill:#438DD5,color:#fff,stroke:#3579B8
    classDef test fill:#2E7BBF,color:#fff,stroke:#24689F
    classDef tool fill:#F5F5F5,color:#333,stroke:#BBB
    class settings,urls,hello,health,server,eurls,eviews,ser,model,mig,exc code
    class tapi,helpers,tops test
    class manage,docker,cfg,ci tool
    style project fill:#E8E8E8,color:#333,stroke:#BBB
    style domain fill:#E8E8E8,color:#333,stroke:#BBB
    style verify fill:#E8E8E8,color:#333,stroke:#BBB
    style tooling fill:#FAFAFA,color:#333,stroke:#CCC
```

### Legend

- **Blue** = application code (project + events domain)
- **Darker blue** = tests
- **Light gray** = runtime and tooling
- **Solid arrow** = request path
- **Dashed arrow** = configuration
- **Dashed line** = verified by tests / run in CI

## Explanation

- **Django project (`backend/`)**: wires everything together. `settings.py` loads the env-driven config and registers the global exception handler. `urls.py` is the root router. `views.py` and `health.py` serve `/` and `/healthz/`. `asgi.py`/`wsgi.py` are the server entry points.
- **Events domain (`events/`)**: holds the API logic. A request flows urls → views → serializers → models, and the serializer validates the input and persists it through the `Event` model. `migrations/` is the schema history, and every DRF error is shaped by `exceptions.py` into `{code, message, details}`.
- **Verification (`tests/`)**: pytest-django suites that call the real HTTP routes. They verify the events API contract, the health endpoint and the hello view. `events_helpers.py` holds the shared constants and helpers.
- **Runtime & tooling**: supports the project without holding any business logic. `manage.py` runs Django commands, Docker runs it locally, `requirements.txt`/`pyproject.toml`/`.env.example` hold its dependencies and config, and CI runs lint, the migration check and pytest on every PR.

## Related Notes

- [[05-Component-Backend]]
- [[Project_Structure]]
