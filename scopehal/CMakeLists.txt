include_directories(${GTKMM_INCLUDE_DIRS} ${SIGCXX_INCLUDE_DIRS})
link_directories(${GTKMM_LIBRARY_DIRS} ${SIGCXX_LIBRARY_DIRS})

find_library(LXI_LIB lxi)

# Additional WinAPI libraries
if(WIN32)
	set(WIN_LIBS shlwapi)
endif()

if(LXI_LIB)
	set(HAS_LXI true)
	set(LXI_LIBRARIES ${LXI_LIB})
else()
	set(HAS_LXI false)
	set(LXI_LIBRARIES "")
endif()

if(OpenCL_FOUND)
	set(HAVE_OPENCL 1)
else()
	set(HAVE_OPENCL 0)
	set(OpenCL_LIBRARIES "")
	set(OpenCL_INCLUDE_DIR "")
endif()

if(CLFFT_FOUND)
	set(HAVE_CLFFT 1)
else()
	set(HAVE_CLFFT 0)
	set(CLFFT_LIBRARIES "")
	set(CLFFT_INCLUDE_DIR "")
endif()

set(SCOPEHAL_SOURCES
	base64.cpp
	scopehal.cpp
	avx_mathfun.cpp

	Unit.cpp

	SCPITransport.cpp
	SCPISocketTransport.cpp
	VICPSocketTransport.cpp
	SCPILxiTransport.cpp
	SCPINullTransport.cpp
	SCPITMCTransport.cpp
	SCPIUARTTransport.cpp
	SCPIDevice.cpp

	IBISParser.cpp
	SParameters.cpp
	TouchstoneParser.cpp

	FlowGraphNode.cpp
	Trigger.cpp
	DropoutTrigger.cpp
	EdgeTrigger.cpp
	GlitchTrigger.cpp
	NthEdgeBurstTrigger.cpp
	PulseWidthTrigger.cpp
	RuntTrigger.cpp
	SerialTrigger.cpp
	SlewRateTrigger.cpp
	TwoLevelTrigger.cpp
	UartTrigger.cpp
	WindowTrigger.cpp

	Instrument.cpp
	FunctionGenerator.cpp
	Oscilloscope.cpp
	OscilloscopeChannel.cpp
	SCPIOscilloscope.cpp
	AgilentOscilloscope.cpp
	AntikernelLabsOscilloscope.cpp
	AntikernelLogicAnalyzer.cpp
	DemoOscilloscope.cpp
	LeCroyOscilloscope.cpp
	MockOscilloscope.cpp
	PicoOscilloscope.cpp
	RigolOscilloscope.cpp
	SiglentSCPIOscilloscope.cpp
	SignalGeneratorOscilloscope.cpp
	TektronixOscilloscope.cpp
	RohdeSchwarzOscilloscope.cpp
	RohdeSchwarzHMC8012Multimeter.cpp
	RohdeSchwarzHMC804xPowerSupply.cpp
	Multimeter.cpp
	PowerSupply.cpp

	Filter.cpp
	FilterParameter.cpp
	PacketDecoder.cpp
	PeakDetectionFilter.cpp
	Statistic.cpp
	SpectrumChannel.cpp

	TestWaveformSource.cpp
	)

configure_file(config.h.in config.h)

add_library(scopehal SHARED
	${SCOPEHAL_SOURCES})
target_link_libraries(scopehal ${SIGCXX_LIBRARIES} ${GTKMM_LIBRARIES} xptools log graphwidget yaml-cpp
	${LXI_LIBRARIES} ${WIN_LIBS} ${LIBFFTS_LIBRARIES} ${OpenCL_LIBRARIES} ${CLFFT_LIBRARIES})

target_include_directories(scopehal
PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_BINARY_DIR}
	${LIBFFTS_INCLUDE_DIR}
	${OpenCL_INCLUDE_DIR}
	${CLFFT_INCLUDE_DIR}
	)

if(${HAS_LXI})
	target_compile_definitions(scopehal PUBLIC HAS_LXI)
endif()

install(TARGETS scopehal LIBRARY DESTINATION /usr/lib)
