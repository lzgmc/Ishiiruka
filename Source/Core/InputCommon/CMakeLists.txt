set(SRCS	ControllerEmu.cpp
			InputConfig.cpp
			UDPWiimote.cpp
     		UDPWrapper.cpp
			ControllerInterface/ControllerInterface.cpp
			ControllerInterface/Device.cpp
			ControllerInterface/ExpressionParser.cpp)
set(LIBS	common)

if(WIN32)
	set(SRCS	${SRCS}
				ControllerInterface/DInput/DInput.cpp
				ControllerInterface/DInput/DInputJoystick.cpp
				ControllerInterface/DInput/DInputKeyboardMouse.cpp
				ControllerInterface/XInput/XInput.cpp
				ControllerInterface/ForceFeedback/ForceFeedbackDevice.cpp)
elseif(APPLE)
	find_library(COREFOUNDATION_LIBRARY CoreFoundation)
	find_library(CARBON_LIBRARY Carbon)
	find_library(COCOA_LIBRARY Cocoa)
	set(SRCS	${SRCS}
				ControllerInterface/OSX/OSX.mm
				ControllerInterface/OSX/OSXKeyboard.mm
				ControllerInterface/OSX/OSXJoystick.mm
				ControllerInterface/Quartz/Quartz.mm
				ControllerInterface/Quartz/QuartzKeyboardAndMouse.mm
				ControllerInterface/ForceFeedback/ForceFeedbackDevice.cpp)
	set(LIBS ${LIBS} ${COREFOUNDATION_LIBRARY} ${CARBON_LIBRARY} ${COCOA_LIBRARY})
elseif(X11_FOUND)
	set(SRCS	${SRCS}
				ControllerInterface/Xlib/XInput2.cpp)
	set(LIBS ${LIBS} ${X11_LIBRARIES} ${XINPUT2_LIBRARIES})
elseif(ANDROID)
	add_definitions(-DCIFACE_USE_ANDROID)
	set(SRCS ${SRCS}
	         ControllerInterface/Android/Android.cpp)
endif()

if(ANDROID)
        set(SRCS        ${SRCS} GCAdapter_Android.cpp)
else()
        set(SRCS        ${SRCS} GCAdapter.cpp)
        set(LIBS ${LIBS} ${LIBUSB_LIBRARIES})
endif()

if(LIBEVDEV_FOUND AND LIBUDEV_FOUND)
	set(SRCS ${SRCS} ControllerInterface/evdev/evdev.cpp)
	set(LIBS ${LIBS} ${LIBEVDEV_LIBRARY} ${LIBUDEV_LIBRARY})
endif()

if(UNIX)
	set(SRCS ${SRCS} ControllerInterface/Pipes/Pipes.cpp)
endif()

if(SDL_FOUND OR SDL2_FOUND)
	set(SRCS ${SRCS} ControllerInterface/SDL/SDL.cpp)
	if (SDL2_FOUND)
		set(LIBS ${LIBS} ${SDL2_LIBRARY})
	elseif(SDL_FOUND)
		set(LIBS ${LIBS} ${SDL_LIBRARY})
	endif()
endif()

add_dolphin_library(inputcommon "${SRCS}" "${LIBS}")
