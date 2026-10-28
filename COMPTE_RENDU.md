# Compte rendu : TP API LogiAI

**Noms :** ............................  **Groupe :** ........

## 1. Environnement et premiers tests
- Sortie de `python -m pytest` au départ (collez-la) :
(.venv) PS C:\Users\User\Downloads\logiai_starter\starter> python -m pytest
========================================================== test session starts ===========================================================
platform win32 -- Python 3.11.9, pytest-9.2.0, pluggy-1.7.0
rootdir: C:\Users\User\Downloads\logiai_starter\starter
plugins: anyio-4.15.1
collected 13 items                                                                                                                        

tests\test_api.py ..FFFF......F                                                                                                     [100%]

================================================================ FAILURES ================================================================
____________________________________________________ test_predict_valide_renvoie_200 _____________________________________________________

    def test_predict_valide_renvoie_200():
        with TestClient(app) as client:
>           assert post(client).status_code == 200
E           assert 422 == 200
E            +  where 422 = <Response [422 Unprocessable Entity]>.status_code
E            +    where <Response [422 Unprocessable Entity]> = post(<starlette.testclient.TestClient object at 0x000002213E6D8B10>)

tests\test_api.py:30: AssertionError
____________________________________________________ test_predict_forme_de_la_reponse ____________________________________________________

    def test_predict_forme_de_la_reponse():
        with TestClient(app) as client:
            data = post(client).json()
>           assert set(data) == {"probabilite_retard", "alerte", "seuil", "model_version"}
E           AssertionError: assert {'detail'} == {'alerte', 'm...ard', 'seuil'}
E             
E             Extra items in the left set:
E             'detail'
E             Extra items in the right set:
E             'model_version'
E             'probabilite_retard'
E             'seuil'
E             'alerte'
E             Use -v to get more diff

tests\test_api.py:36: AssertionError
_________________________________________________ test_predict_probabilite_entre_0_et_1 __________________________________________________

    def test_predict_probabilite_entre_0_et_1():
        with TestClient(app) as client:
>           assert 0 <= post(client).json()["probabilite_retard"] <= 1
                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
E           KeyError: 'probabilite_retard'

tests\test_api.py:41: KeyError
________________________________________ test_predict_trajet_risque_plus_eleve_que_trajet_facile _________________________________________

    def test_predict_trajet_risque_plus_eleve_que_trajet_facile():
        facile = dict(distance_km=5, poids_kg=3, heure_depart=11, pluie_mm=0,
                      trafic_index=1, nb_arrets=0, type_vehicule="moto")
        with TestClient(app) as client:
>           p_difficile = post(client).json()["probabilite_retard"]
                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
E           KeyError: 'probabilite_retard'

tests\test_api.py:48: KeyError
_____________________________________________________ test_modele_absent_renvoie_503 _____________________________________________________

    def test_modele_absent_renvoie_503():
        with TestClient(app) as client:
            state.pop("predictor", None)  # on simule un modèle non chargé
>           assert post(client).status_code == 503
E           assert 422 == 503
E            +  where 422 = <Response [422 Unprocessable Entity]>.status_code
E            +    where <Response [422 Unprocessable Entity]> = post(<starlette.testclient.TestClient object at 0x0000022148584110>)

tests\test_api.py:87: AssertionError
============================================================ warnings summary ============================================================
..\Atelier_3\.venv\Lib\site-packages\fastapi\testclient.py:1
  C:\Users\User\Downloads\logiai_starter\Atelier_3\.venv\Lib\site-packages\fastapi\testclient.py:1: StarletteDeprecationWarning: Using `httpx` with `starlette.testclient` is deprecated; install `httpx2` instead.
    from starlette.testclient import TestClient as TestClient  # noqa

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
======================================================== short test summary info =========================================================
FAILED tests/test_api.py::test_predict_valide_renvoie_200 - assert 422 == 200
FAILED tests/test_api.py::test_predict_forme_de_la_reponse - AssertionError: assert {'detail'} == {'alerte', 'm...ard', 'seuil'}
FAILED tests/test_api.py::test_predict_probabilite_entre_0_et_1 - KeyError: 'probabilite_retard'
FAILED tests/test_api.py::test_predict_trajet_risque_plus_eleve_que_trajet_facile - KeyError: 'probabilite_retard'
FAILED tests/test_api.py::test_modele_absent_renvoie_503 - assert 422 == 503
================================================= 5 failed, 8 passed, 1 warning in 8.02s =================================================
- Pourquoi certains tests qui attendent un 422 passent-ils déjà, alors que rien n'est écrit ?

## 2. Mon schéma
- Mes bornes et leur justification physique (une ligne par variable) :

| Variable | Min | Max | Pourquoi |
|---|---|---|---|
| distance_km | 1 | 1000 | |
| poids_kg | | | |
| heure_depart | | | |
| pluie_mm | | | |
| trafic_index | | | |
| nb_arrets | | | |

## 3. Ma fonction predict()
(collez le code)

## 4. Quatre requêtes
| Requête | Code obtenu | Pourquoi |
|---|---|---|
| livraison normale | | |
| distance_km = -5 | | |
| type_vehicule = "avion" | | |
| modèle absent (supprimez models/ et relancez) | | |

## 5. Questions de compréhension
1. Pourquoi charge-t-on le modèle au démarrage et pas à chaque requête ?
2. Qui écrit le contrôle « pluie <= 100 », et où est-il écrit ?
3. Différence entre 422 et 503 ?
4. Pourquoi une route `/v1/predict` plutôt que `/predict` ?
5. Que se passerait-il si la distance était déclarée `int` ?

## 6. Décision : modèle absent
Répondre 503 ou refuser de démarrer ? Qu'est-ce que chaque option fait perdre ?

## 7. Lien de la pull request
