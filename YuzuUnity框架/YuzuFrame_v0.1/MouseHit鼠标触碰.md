使用MVC架构，将其拆分为MouseHitModel、MouseHitController、MouseHitView，使用时只需要在Unity对象中挂载MouseHitController.cs即可。
```C#
using UnityEngine;  
  
public class MouseHitModel  
{  
    public struct HitInfo  
    {  
        public string name;  
        public Vector2 point;  
        public bool hasHit;  
    }  
    private LayerMask layerMask;  
    private HitInfo lastHit;  
  
    public MouseHitModel(string layerName)  
    {        
	    layerMask = LayerMask.GetMask(layerName);  
    }  
    /// 业务核心：执行 2D 射线检测  
    public HitInfo ShootRay(Vector2 worldPos)  
    {        
	    RaycastHit2D hit = Physics2D.Raycast(worldPos, Vector2.zero, 0f, layerMask);  
        lastHit = new HitInfo  
        {  
            hasHit = hit.collider != null,  
            name   = hit.collider ? hit.collider.name : "",  
            point  = hit.point  
        };  
        return lastHit;  
    }  
    public HitInfo GetLastHit() => lastHit;  
}
```

```C#
using UnityEngine;  
  
public class MouseHitController : MonoBehaviour  
{  
    private MouseHitModel model;  
    private MouseHitView  view;  
  
    void Awake()  
    {        model = new MouseHitModel("MouseObject");  
        view  = new MouseHitView();  
    }  
    void Update()  
    {        
	    if (Input.GetMouseButtonDown(0))  
        {            
	        Vector2 worldPos = Camera.main.ScreenToWorldPoint(Input.mousePosition);  
            var info = model.ShootRay(worldPos);  
            view.ShowHit(info);  
        }    }}
```

```C#
using UnityEngine;  
  
public class MouseHitView  
{  
    public void ShowHit(in MouseHitModel.HitInfo info)  
    {        if (info.hasHit)  
        {            Debug.Log($"[View] Hit: {info.name}");  
            Debug.Log($"[View] Hit Point: {info.point}");  
        }        else  
        {  
            Debug.Log("[View] No hit detected.");  
        }    }}
```