# Vulkan-CPP

Vulkan abstraction layer that focuses on making an attempt at simplifying API design to utilize vulkan, with minimal cost and allows ability to flexibly use the API in your own projects.

# Overview

This vulkan abstract layer does not make an attempt at trying to abstract and hide how the vulkan physical and logical devices get created per-say, but what this abstraction allows you to do is focus on how you want those devices to be configured, adapted, and flexible with your specific implementations.

What this means is yes, the physical and logical devices are not quite hidden from you as the developer, but it allows you to still be able to control how they are configured and add your own checks through the API. Look at the code examples below for more information.

## How to Build

This project uses the C++ conan package manager to manage dependencies. Reference to [getting started](https://engine3d-dev.github.io/0.1/getting_started) to setup the development environment.

The setup is the same across all vulkan-related projects in the atlas organization.

## Examples

The examples are still currently in the works, here are some examples of the potential API designs for vulkan-cpp to get working.

### Setting up Vulkan Instance

This code example can be used for setting up the vulkan API with `vk::instance`, including setting up application-related information the users should be concerned with.

```C++

#include <vulkan-cpp/vk_instance.hpp>

// dummy debug callback
static VKAPI_ATTR VkBool32 VKAPI_CALL
debug_callback(VkDebugUtilsMessageSeverityFlagBitsEXT messageSeverity
  VkDebugUtilsMessageTypeFlagsEXT messageType
  const VkDebugUtilsMessengerCallbackDataEXT* pCallbackData
  void* pUserData
);

int main() {
  // setting up validation layers
  std::array<const char*, 2> validation_layers = {
    "VK_LAYER_KHRONOS_validation"
  };

  // setting up extensions
  std::array<const char*, 2> global_extensions = {
    "VK_LAYER_KHRONOS_validation"
  };

  vk::debug_message_utility debug_callback_info = {
    .severity = vk::verbose | vk::warning | vk::error,
    . message_type = vk::general | vk::validation | vk::performance
    .callback = debug_callback
  };

  vk::application_configuration config = {
    .app_name = "Vulkan-HelloWorld",
    .version = vk::version_1_3, // specify to using vulkan 1.3
    .validation = validation_layers // .validation takes in a std::span<const char*>
    .extensions = global_extensions // .extensions also takes in std::span<const char*>
  };

  vk::instance init_vk_instance = vk::instance(config, debug_message_utility);

  // If you need to get the validation layers currently supported, this is how you do it.
  // If built in debug mode, this will actually contain data, if built in release. The std::span<vk::layer_properties> .empty() would be true.
  std::span<vk::layer_properties> available_validation_layers = init_vk_instance.validation_layers();

  // do stuff with available_validation_layers
}
```

