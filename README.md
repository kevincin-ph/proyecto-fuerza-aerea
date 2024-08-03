# proyecto-fuerza-aerea
#include <fstream>
#include <iostream>
#include <opencv2/opencv.hpp>
#include <openssl/aes.h>
#include <openssl/rand.h>
#include <vector>

#define AES_BLOCK_SIZE 16

// Función para generar una llave aleatoria de 128 bits (AES)
std::vector<unsigned char> generarLlave() {
  std::vector<unsigned char> llave(AES_BLOCK_SIZE);
  if (!RAND_bytes(llave.data(), AES_BLOCK_SIZE)) {
    throw std::runtime_error("Error al generar la llave");
  }
  return llave;
}

// Función para leer una imagen en un formato específico utilizando OpenCV
cv::Mat leerImagen(const std::string &rutaEntrada, const std::string &formato) {
  cv::Mat imagen = cv::imread(rutaEntrada, cv::IMREAD_UNCHANGED);
  if (imagen.empty()) {
    throw std::runtime_error("Error al cargar la imagen");
  }
  return imagen;
}

// Función para escribir una imagen en un formato específico utilizando OpenCV
void escribirImagen(const cv::Mat &imagen, const std::string &rutaSalida,
                    const std::string &formato) {
  std::vector<unsigned char> buffer;
  cv::imencode("." + formato, imagen, buffer);
  std::ofstream archivoSalida(rutaSalida, std::ios::binary);
  archivoSalida.write(reinterpret_cast<char *>(buffer.data()), buffer.size());
  if (!archivoSalida) {
    throw std::runtime_error("Error al guardar la imagen");
  }
}

// Función para cifrar una imagen utilizando AES en modo CBC
void cifrarImagen(const std::string &rutaEntrada, const std::string &rutaSalida,
                  const std::string &formatoEntrada,
                  const std::string &formatoSalida,
                  const std::vector<unsigned char> &llave) {
  // Leer la imagen en formato especificado
  cv::Mat imagenOriginal = leerImagen(rutaEntrada, formatoEntrada);

  // Convertir la imagen a un vector de bytes para el cifrado
  std::vector<uchar> imagenBytes;
  cv::imencode("." + formatoEntrada, imagenOriginal, imagenBytes);

  // Inicializar el contexto de cifrado AES en modo CBC
  AES_KEY llaveAES;
  AES_set_encrypt_key(llave.data(), 128, &llaveAES);
  unsigned char iv[AES_BLOCK_SIZE]; // Vector de inicialización (IV)
  memset(iv, 0, AES_BLOCK_SIZE);    // Inicializar IV a ceros (por simplicidad)

  // Cifrar la imagen por bloques
  std::vector<unsigned char> imagenCifrada(imagenBytes.size());
  AES_cbc_encrypt(imagenBytes.data(), imagenCifrada.data(), imagenBytes.size(),
                  &llaveAES, iv, AES_ENCRYPT);

  // Convertir los datos cifrados nuevamente a una imagen OpenCV y guardarla
  cv::Mat imagenCifradaMat = cv::imdecode(imagenCifrada, cv::IMREAD_UNCHANGED);
  escribirImagen(imagenCifradaMat, rutaSalida, formatoSalida);
}

// Función para descifrar una imagen utilizando AES en modo CBC
void descifrarImagen(const std::string &rutaEntrada,
                     const std::string &rutaSalida,
                     const std::string &formatoEntrada,
                     const std::string &formatoSalida,
                     const std::vector<unsigned char> &llave) {
  // ... (Código similar a cifrarImagen, pero utilizando AES_cbc_decrypt)
}

int main(int argc, char *argv[]) {
  // ... (Manejo de argumentos, similar al código anterior)

  cifrarImagen("imagenOriginal.png", "imagenCifrada.enc", "png", "jpeg", llave);
  descifrarImagen("imagenCifrada.enc", "imagenDescifrada.png", "jpeg", "png",
                  llave);

  return 0;
}
#include <fstream>
#include <iostream>
#include <opencv2/opencv.hpp>
#include <openssl/aes.h>
#include <openssl/rand.h>
#include <vector>

#define AES_BLOCK_SIZE 16

// Función para generar una llave aleatoria de 128 bits (AES)
std::vector<unsigned char> generarLlave() {
  std::vector<unsigned char> llave(AES_BLOCK_SIZE);
  if (!RAND_bytes(llave.data(), AES_BLOCK_SIZE)) {
    throw std::runtime_error("Error al generar la llave");
  }
  return llave;
}

// Función para leer una imagen en un formato específico utilizando OpenCV
cv::Mat leerImagen(const std::string &rutaEntrada, const std::string &formato) {
  cv::Mat imagen = cv::imread(rutaEntrada, cv::IMREAD_UNCHANGED);
  if (imagen.empty()) {
    throw std::runtime_error("Error al cargar la imagen");
  }
  return imagen;
}

// Función para escribir una imagen en un formato específico utilizando OpenCV
void escribirImagen(const cv::Mat &imagen, const std::string &rutaSalida,
                    const std::string &formato) {
  std::vector<unsigned char> buffer;
  cv::imencode("." + formato, imagen, buffer);
  std::ofstream archivoSalida(rutaSalida, std::ios::binary);
  archivoSalida.write(reinterpret_cast<char *>(buffer.data()), buffer.size());
  if (!archivoSalida) {
    throw std::runtime_error("Error al guardar la imagen");
  }
}

// Función para cifrar una imagen utilizando AES en modo CBC
void cifrarImagen(const std::string &rutaEntrada, const std::string &rutaSalida,
                  const std::string &formatoEntrada,
                  const std::string &formatoSalida,
                  const std::vector<unsigned char> &llave) {
  // Leer la imagen en formato especificado
  cv::Mat imagenOriginal = leerImagen(rutaEntrada, formatoEntrada);

  // Convertir la imagen a un vector de bytes para el cifrado
  std::vector<uchar> imagenBytes;
  cv::imencode("." + formatoEntrada, imagenOriginal, imagenBytes);

  // Inicializar el contexto de cifrado AES en modo CBC
  AES_KEY llaveAES;
  AES_set_encrypt_key(llave.data(), 128, &llaveAES);
  unsigned char iv[AES_BLOCK_SIZE]; // Vector de inicialización (IV)
  memset(iv, 0, AES_BLOCK_SIZE);    // Inicializar IV a ceros (por simplicidad)

  // Cifrar la imagen por bloques
  std::vector<unsigned char> imagenCifrada(imagenBytes.size());
  AES_cbc_encrypt(imagenBytes.data(), imagenCifrada.data(), imagenBytes.size(),
                  &llaveAES, iv, AES_ENCRYPT);

  // Convertir los datos cifrados nuevamente a una imagen OpenCV y guardarla
  cv::Mat imagenCifradaMat = cv::imdecode(imagenCifrada, cv::IMREAD_UNCHANGED);
  escribirImagen(imagenCifradaMat, rutaSalida, formatoSalida);
}

// Función para descifrar una imagen utilizando AES en modo CBC
void descifrarImagen(const std::string &rutaEntrada,
                     const std::string &rutaSalida,
                     const std::string &formatoEntrada,
                     const std::string &formatoSalida,
                     const std::vector<unsigned char> &llave) {
  // Leer la imagen cifrada en formato especificado
  cv::Mat imagenCifrada = leerImagen(rutaEntrada, formatoEntrada);

  // Convertir la imagen cifrada a un vector de bytes para el descifrado
  std::vector<uchar> imagenCifradaBytes;
  cv::imencode("." + formatoEntrada, imagenCifrada, imagenCifradaBytes);

  // Inicializar el contexto de descifrado AES en modo CBC
  AES_KEY llaveAES;
  AES_set_decrypt_key(llave.data(), 128, &llaveAES);
  unsigned char iv[AES_BLOCK_SIZE]; // Vector de inicialización (IV)
  memset(iv, 0, AES_BLOCK_SIZE);    // Inicializar IV a ceros (por simplicidad)

  // Descifrar la imagen por bloques
  std::vector<unsigned char> imagenDescifrada(imagenCifradaBytes.size());
  AES_cbc_decrypt(imagenCifradaBytes.data(), imagenDescifrada.data(),
                  imagenCifradaBytes.size(), &llaveAES, iv, AES_DECRYPT);

  // Convertir los datos descifrados nuevamente a una imagen OpenCV y guardarla
  cv::Mat imagenDescifradaMat =
      cv::imdecode(imagenDescifrada, cv::IMREAD_UNCHANGED);
  escribirImagen(imagenDescifradaMat, rutaSalida, formatoSalida);
}

int main(int argc, char *argv[]) {
  // Generar una llave aleatoria
  std::vector<unsigned char> llave = generarLlave();

  // Cifrar la imagen original
  cifrarImagen("imagenOriginal.png", "imagenCifrada.enc", "png", "jpeg", llave);

  // Descifrar la imagen cifrada
  descifrarImagen("imagenCifrada.enc", "imagenDescifrada.png", "jpeg", "png",
                  llave);

  return 0;
}
