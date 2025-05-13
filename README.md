# Conversor-de-monedas

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Scanner;

import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class ConversorMonedasApp {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int opcion;

        System.out.println("**********************************************");
        System.out.println("\u001B[1mSea bienvenido/a al Conversor de Moneda =]\u001B[0m");

        do {
            System.out.println("\n1) Dólar => Peso argentino");
            System.out.println("2) Peso argentino => Dólar");
            System.out.println("3) Dólar => Real brasileño");
            System.out.println("4) Real brasileño => Dólar");
            System.out.println("5) Dólar => Peso colombiano");
            System.out.println("6) Peso colombiano => Dólar");
            System.out.println("7) Salir");
            System.out.print("\nElija una opción válida: ");

            opcion = scanner.nextInt();

            if (opcion >= 1 && opcion <= 6) {
                System.out.print("Ingrese la cantidad que desea convertir: ");
                double cantidad = scanner.nextDouble();

                String base = "", destino = "";
                switch (opcion) {
                    case 1 -> { base = "USD"; destino = "ARS"; }
                    case 2 -> { base = "ARS"; destino = "USD"; }
                    case 3 -> { base = "USD"; destino = "BRL"; }
                    case 4 -> { base = "BRL"; destino = "USD"; }
                    case 5 -> { base = "USD"; destino = "COP"; }
                    case 6 -> { base = "COP"; destino = "USD"; }
                }

                convertirMoneda(base, destino, cantidad);
            }

        } while (opcion != 7);

        System.out.println("Gracias por usar el conversor. ¡Hasta pronto!");
        scanner.close();
    }

    public static void convertirMoneda(String base, String destino, double cantidad) {
        String API_KEY = 0f1e8e8e9d0634fc952b3ad2; // <-- Rellena con tu API Key de exchangerate-api
        String url = "https://v6.exchangerate-api.com/v6/" + API_KEY + "/latest/" + base;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .build();

        try {
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            JsonObject json = JsonParser.parseString(response.body()).getAsJsonObject();

            if (!json.get("result").getAsString().equals("success")) {
                System.out.println("Error en la solicitud: " + json.get("result").getAsString());
                return;
            }

            JsonObject rates = json.getAsJsonObject("conversion_rates");
            double tasa = rates.get(destino).getAsDouble();
            double convertido = cantidad * tasa;

            System.out.printf("\n1 %s = %.4f %s\n", base, tasa, destino);
            System.out.printf("%.2f %s = %.2f %s\n\n", cantidad, base, convertido, destino);

        } catch (Exception e) {
            System.out.println("Ocurrió un error al conectar con la API: " + e.getMessage());
        }
    }
}  
