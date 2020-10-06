# Dia 05-10-2020
## #Leer contenido json desde una web

``` Powershell

$routes = @{
    "/" = { return '{
  "alumnos": {
    "alumno": [
      {
        "operacion": "1",
        "usuario": "juanito"
      },
      {
        "operacion": "2",
        "usuario": "luis"
      }
    ]
  }
}' }
}

$url = 'http://localhost:8082/'
$listener = New-Object System.Net.HttpListener
$listener.Prefixes.Add($url)
$listener.Start()

Write-Host "Funcionando $url..."

while ($listener.IsListening)
{
    $context = $listener.GetContext()
    $requestUrl = $context.Request.Url
    $con
    $response = $context.Response

    Write-Host ''
    Write-Host "Petición: $requestUrl"

    $localPath = $requestUrl.LocalPath
    $route = $routes.Get_Item($requestUrl.LocalPath)

    if ($route -eq $null)
    {
        $response.StatusCode = 404
    }
    else
    {
        $content = & $route
        $buffer = [System.Text.Encoding]::UTF8.GetBytes($content)
        $response.ContentLength64 = $buffer.Length
        $response.OutputStream.Write($buffer, 0, $buffer.Length)
    }
    
    $response.Close()

    $responseStatus = $response.StatusCode
    Write-Host "Respuesta: $responseStatus"
}

``` 

## Abrimos este Powershell y lo ejecutamos

``` Powershell

$web = iwr "http://localhost:8082/"

$web.RawContent > .\fichero22.txt
$alumnos = (gc .\fichero22.txt)[5..$web.RawContent.Length] | ConvertFrom-Json  

$alumnos.alumnos.alumno | %{
    $_.operacion
    $_.usuario
}
```
