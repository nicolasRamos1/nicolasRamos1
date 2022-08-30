using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;
using TMPro;
using UnityEngine.SceneManagement;

public class CanvasManager : MonoBehaviour
{
    public Button salirBtn;

    private void Start()
    {
        if (salirBtn)
        {
            salirBtn.onClick.RemoveAllListeners();
            salirBtn.onClick.AddListener(QuitGame);
        }
        if (coloresFigurasBtn)
        {
            coloresFigurasBtn.onClick.RemoveAllListeners();
            coloresFigurasBtn.onClick.AddListener(RandomizarColoresFiguras);
        }
        if (figuraBtn)
        {
            figuraBtn.onClick.RemoveAllListeners();
            figuraBtn.onClick.AddListener(CambiarFigura);
        }
        FiguraDropDownStart();

        if (colorSiluetaBtn)
        {
            colorSiluetaBtn.onClick.RemoveAllListeners();
            colorSiluetaBtn.onClick.AddListener(CambiaColorSilueta);
        }
        ColorSiluetaDropDownStart();

        if (fondoBtn)
        {
            fondoBtn.onClick.RemoveAllListeners();
            fondoBtn.onClick.AddListener(Cambiarfondo);
        }
        FondoDropDownStart();

        if (tiempoBtn)
        {
            tiempoBtn.onClick.RemoveAllListeners();
            tiempoBtn.onClick.AddListener(CambiarTiempo);
        }
        TiempoDropDownStart();

        empezarBtn.onClick.RemoveAllListeners();
        empezarBtn.onClick.AddListener(Empezar);

        if (ConfigGame.instance && ConfigGame.instance.colorSilueta != "")
        {
            for (int i = 0; i < imagenes.Count; i++)
            {
                imagenes[i].color = ConfigGame.instance.colorFiguras[i];
            }
            
            SelectFig(figuras.FindIndex(x=> x == ConfigGame.instance.figura));
            SelectColorSilueta(coloresSilueta.FindIndex(x=>x == ConfigGame.instance.colorSilueta));
            SelectFondo(fondos.FindIndex(x=>x == ConfigGame.instance.fondo));
            SelectTiempo(tiempo.FindIndex(x => x == ConfigGame.instance.tiempo));
        }
        else
        {
            RandomizarColoresFiguras();
            SelectFig(0);
            SelectColorSilueta(0);
            SelectFondo(0);
            SelectTiempo(0);
        }
        
    }

    #region color figuras

    [Header("color figuras settings")]
    public bool randomColors;
    public List<Image> imagenes;
    public List<bool> abierto;
    public List<Color> colores;
    [HideInInspector] public List<bool> colorAsignado;
    public Button coloresFigurasBtn;

    void RandomizarColoresFiguras()
    {
        if (randomColors)
        {
            colorAsignado.Clear();
            ConfigGame.instance.colorFiguras.Clear();
            for (int i = 0; i < imagenes.Count; i++)
            {
                colorAsignado.Add(false);
                OcultarColores(i);
            }
            for (int i = 0; i < imagenes.Count; i++)
            {
                int random = Random.Range(0, colores.Count - 1);
                while (colorAsignado[random])
                {
                    random = Random.Range(0, colores.Count - 1);
                }

                imagenes[i].color = colores[random];
                colorAsignado[random] = true;
                ConfigGame.instance.colorFiguras.Add(colores[random]);
            }
        }
        else
        {
            colorAsignado.Clear();
            ConfigGame.instance.colorFiguras.Clear();
            for (int i = 0; i < imagenes.Count; i++)
            {
                colorAsignado.Add(false);
                OcultarColores(i);
            }
            for (int i = 0; i < imagenes.Count; i++)
            {
                imagenes[i].color = colores[i];
                colorAsignado[i] = true;
                ConfigGame.instance.colorFiguras.Add(colores[i]);
            }
        }
    }
    int idImagenAnterior;
    public void DesplegarColores(int idImagen)
    {
        if (abierto[idImagen])
        {
            Transform content = imagenes[idImagen].transform.GetChild(0);
            OcultarColores(idImagen);
            abierto[idImagen] = false;

        }
        else
        {
            Transform content = imagenes[idImagen].transform.GetChild(0);
            OcultarColores(idImagen);
            OcultarColores(idImagenAnterior);

            for (int i = 0; i < colores.Count; i++)
            {
                GameObject go = Instantiate(content.GetChild(0).gameObject, content);
                go.SetActive(true);
                go.GetComponent<ColorSelect>().StartComponent(colores[i], idImagen, this);
            }
            abierto[idImagen] = true;
            idImagenAnterior = idImagen;
        }
        
    }
    public void SeleccionarColor(int idImagen,Color color)
    {
        imagenes[idImagen].color = color;
        ConfigGame.instance.colorFiguras[idImagen] = color;
        OcultarColores(idImagen);
    }
    void OcultarColores(int idImagen)
    {
        if (imagenes[idImagen].transform.childCount != 0)
        {
            Transform content = imagenes[idImagen].transform.GetChild(0);
            for (int i = 1; i < content.childCount; i++)
            {
                Destroy( content.GetChild(i).gameObject);
            }
        }
        abierto[idImagenAnterior] = false;
    }

    #endregion

    #region figura

    [Header("figura settings")]

    public Button figuraBtn;
    public List<string> figuras;
    public TextMeshProUGUI figuraTxt;
    int rFiguras;

    public TMP_Dropdown figuraDropDown;
    public void FiguraDropDownStart()
    {
        figuraDropDown.ClearOptions();
        figuraDropDown.AddOptions(figuras);
    }

    public void FiguraDropDownSelect()
    {
        SelectFig(figuraDropDown.value);
    }
    public void CambiarFigura()
    {
        rFiguras++;
        if (rFiguras >= figuras.Count)
        {
            rFiguras = 0;
        }
        SelectFig(rFiguras);
    }
    void SelectFig(int id)
    {
        ConfigGame.instance.figura = figuras[id];
        figuraTxt.text = figuras[id];
        figuraDropDown.value = id;
        rFiguras = id;
    }

    #endregion

    #region colorSilueta

    [Header("colorSilueta settings")]

    public Button colorSiluetaBtn;
    public List<string> coloresSilueta;
    public TextMeshProUGUI colorSiluetaTxt;
    int rColorSilueta;

    public TMP_Dropdown colorSiluetaDropDown;
    public void ColorSiluetaDropDownStart()
    {
        colorSiluetaDropDown.ClearOptions();
        colorSiluetaDropDown.AddOptions(coloresSilueta);
    }

    public void ColorSiluetaDropDownSelect()
    {
        SelectColorSilueta(colorSiluetaDropDown.value);
    }

    public void CambiaColorSilueta()
    {
        rColorSilueta++;
        if (rColorSilueta >= coloresSilueta.Count)
        {
            rColorSilueta = 0;
        }
        
        SelectColorSilueta(rColorSilueta);
    }
    void SelectColorSilueta(int id)
    {
        ConfigGame.instance.colorSilueta = coloresSilueta[id];
        colorSiluetaTxt.text = coloresSilueta[id];
        colorSiluetaDropDown.value = id;
        rColorSilueta = id;
    }

    #endregion

    #region fondo

    [Header("fondo settings")]
    
    public Button fondoBtn;
    public List<string> fondos;
    public TextMeshProUGUI fondoTxt;
    int rFondo;

    public TMP_Dropdown fondoDropDown;
    public void FondoDropDownStart()
    {
        fondoDropDown.ClearOptions();
        fondoDropDown.AddOptions(fondos);
    }

    public void FondoDropDownSelect()
    {
        SelectFondo(fondoDropDown.value);
    }

    public void Cambiarfondo()
    {
        rFondo++;
        if (rFondo >= fondos.Count)
        {
            rFondo = 0;
        }
        SelectFondo(rFondo);
    }

    void SelectFondo(int id)
    {
        ConfigGame.instance.fondo = fondos[id];
        fondoTxt.text = fondos[id];
        fondoDropDown.value = id;
        rFondo = id;
    }
    #endregion

    #region tiempo

    [Header("tiempo settings")]

    public Button tiempoBtn;
    public List<float> tiempo;
    public TextMeshProUGUI tiempoTxt;
    int rTiempo;

    public TMP_Dropdown tiempoDropDown;
    public void TiempoDropDownStart()
    {
        tiempoDropDown.ClearOptions();

        List<string> tiemposStr = new List<string>();
        for (int i = 0; i < tiempo.Count; i++)
        {
            tiemposStr.Add(tiempo[i].ToString());
        }
        tiempoDropDown.AddOptions(tiemposStr);
    }

    public void TiempoDropDownSelect()
    {
        SelectTiempo(tiempoDropDown.value);
    }

    public void CambiarTiempo()
    {
        rTiempo++;
        if (rTiempo >= tiempo.Count)
        {
            rTiempo = 0;
        }

        SelectTiempo(rTiempo);
    }
    void SelectTiempo(int id)
    {
        ConfigGame.instance.tiempo = tiempo[id];
        tiempoTxt.text = tiempo[id].ToString() + "s";
        tiempoDropDown.value = id;
        rTiempo = id;
    }

    #endregion

    public Button empezarBtn;
    public string sufijo = "Mono";
    public void Empezar()
    {
        ConfigGame cg = ConfigGame.instance;
        if (cg.colorSilueta != "" && cg.figura != "" && cg.fondo != "")
        {
            SceneManager.LoadScene(ConfigGame.instance.figura + sufijo);
        }
    }


    void QuitGame()
    {
        Application.Quit();
        Debug.Log("Salir");
    }
    public void LimpiarVista()
    {
        OcultarColores(idImagenAnterior);
    }
}
