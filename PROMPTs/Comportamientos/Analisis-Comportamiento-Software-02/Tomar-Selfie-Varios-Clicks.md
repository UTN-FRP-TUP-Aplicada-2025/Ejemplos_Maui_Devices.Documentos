07-17 12:56:18.452 W/OnBackInvokedCallback(27418): OnBackInvokedCallback is not enabled for the application.
07-17 12:56:18.452 W/OnBackInvokedCallback(27418): Set 'android:enableOnBackInvokedCallback="true"' in the application manifest.
07-17 12:56:18.545 W/MaterialButton(27418): MaterialButton manages its own background to control elevation, shape, color and states. Consider using backgroundTint, shapeAppearance and other attributes where available. A custom background will ignore these attributes and you should consider handling interaction states such as pressed, focused and disabled
07-17 12:56:18.605 W/MaterialButton(27418): MaterialButton manages its own background to control elevation, shape, color and states. Consider using backgroundTint, shapeAppearance and other attributes where available. A custom background will ignore these attributes and you should consider handling interaction states such as pressed, focused and disabled
07-17 12:56:18.623 W/MaterialButton(27418): MaterialButton manages its own background to control elevation, shape, color and states. Consider using backgroundTint, shapeAppearance and other attributes where available. A custom background will ignore these attributes and you should consider handling interaction states such as pressed, focused and disabled
07-17 12:56:18.640 W/MaterialButton(27418): MaterialButton manages its own background to control elevation, shape, color and states. Consider using backgroundTint, shapeAppearance and other attributes where available. A custom background will ignore these attributes and you should consider handling interaction states such as pressed, focused and disabled
07-17 12:56:18.659 W/MaterialButton(27418): MaterialButton manages its own background to control elevation, shape, color and states. Consider using backgroundTint, shapeAppearance and other attributes where available. A custom background will ignore these attributes and you should consider handling interaction states such as pressed, focused and disabled
07-17 12:56:18.975 D/Recorder(27418): mRequiredFreeStorageBytes = 50 MB
07-17 12:56:19.151 W/CXCP    (27418): Detaching [] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>> (Ignored)
07-17 12:56:19.152 D/ResolvedFeatureGroup(27418): resolveFeatureGroup: sessionConfig = SessionConfig@8acc0ef {useCases=[Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6, ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb], frameRateRange=[0, 0], requiredFeatureGroup=[], preferredFeatureGroup=[], effects=[], viewPort=null}, lensFacing = 0
07-17 12:56:19.152 D/CameraUseCaseAdapter(27418): addUseCases: appUseCasesToAdd = [Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6, ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb], featureGroup = null
07-17 12:56:19.152 D/CXCP    (27418): Creating config for PREVIEW
07-17 12:56:19.155 D/CXCP    (27418): Creating config for IMAGE_CAPTURE
07-17 12:56:19.157 D/CXCP    (27418): Creating config for PREVIEW
07-17 12:56:19.160 D/CXCP    (27418): Creating config for IMAGE_CAPTURE
07-17 12:56:19.163 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6
07-17 12:56:19.164 D/SupportedOutputSizesCollector(27418): useCaseConfig = androidx.camera.core.impl.PreviewConfig@c3127fc, candidateSizes = [2320x1740, 2320x1304, 2320x1044, 1920x1080, 1600x1200, 1920x864, 1440x1080, 1600x900, 1280x960, 1600x720, 1558x720, 1516x720, 1280x720, 1024x768, 1280x576, 960x720, 960x540, 960x432, 720x480, 640x480, 352x288, 320x240, 240x320, 256x192, 176x144]
07-17 12:56:19.176 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb
07-17 12:56:19.177 D/SupportedOutputSizesCollector(27418): useCaseConfig = androidx.camera.core.impl.ImageCaptureConfig@52e7d0b, candidateSizes = [2320x1740, 2320x1304, 2320x1044, 1920x1080, 1600x1200, 1920x864, 1440x1080, 1600x900, 1280x960, 1600x720, 1558x720, 1516x720, 1280x720, 1024x768, 1280x576, 960x720, 960x540, 960x432, 720x480, 640x480, 352x288, 320x240, 240x320, 256x192, 176x144]
07-17 12:56:19.182 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6
07-17 12:56:19.183 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb
07-17 12:56:19.185 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6
07-17 12:56:19.186 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb
07-17 12:56:19.187 D/CXCP    (27418): DynamicRangeResolver: Resolved dynamic range for use case androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6 to no compatible HDR dynamic ranges.
07-17 12:56:19.187 D/CXCP    (27418): DynamicRange@2df4d8f{encoding=UNSPECIFIED, bitDepth=0}
07-17 12:56:19.187 D/CXCP    (27418): ->
07-17 12:56:19.187 D/CXCP    (27418): DynamicRange@91a84ee{encoding=SDR, bitDepth=8}
07-17 12:56:19.188 D/CXCP    (27418): resolvedDynamicRanges = {androidx.camera.core.impl.ImageCaptureConfig@52e7d0b=DynamicRange@91a84ee{encoding=SDR, bitDepth=8}, androidx.camera.core.impl.PreviewConfig@c3127fc=DynamicRange@91a84ee{encoding=SDR, bitDepth=8}}
07-17 12:56:19.188 D/CXCP    (27418): getSuggestedStreamSpecifications: isPreviewStabilizationSupported = false, isFeatureComboInvocation = false
07-17 12:56:19.188 D/CXCP    (27418): resolveSpecsByCheckingMethod: checkingMethod = WITHOUT_FEATURE_COMBO
07-17 12:56:19.188 D/CXCP    (27418): resolveSpecsBySettings: featureSettings = FeatureSettings(cameraMode=0, requiredMaxBitDepth=8, hasVideoCapture=false, videoStabilization=UNSPECIFIED, isUltraHdrOn=false, isHighSpeedOn=false, isFeatureComboInvocation=false, requiresFeatureComboQuery=false, targetFpsRange=[0, 0], isStrictFpsRequired=false)
07-17 12:56:19.191 D/CameraQuirks(27418): camera2 CameraQuirks = CloseCaptureSessionOnVideoQuirk | FinalizeSessionOnCloseQuirk
07-17 12:56:19.191 D/CameraQuirks(27418): camera2 CameraQuirks = CloseCaptureSessionOnVideoQuirk | FinalizeSessionOnCloseQuirk
07-17 12:56:19.193 D/CXCP    (27418): resolveSpecsBySettings: bestSizesAndFps = BestSizesAndMaxFpsForConfigs(bestSizes=[2320x1740, 1600x1200], bestSizesForStreamUseCase=null, maxFpsForBestSizes=30, maxFpsForStreamUseCase=2147483647, maxFpsForAllSizes=2147483647)
07-17 12:56:19.193 W/CXCP    (27418): Detaching [] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>> (Ignored)
07-17 12:56:19.194 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6
07-17 12:56:19.194 D/Preview (27418): onSuggestedStreamSpecUpdated: primaryStreamSpec = StreamSpec{resolution=1600x1200, originalConfiguredResolution=1600x1200, dynamicRange=DynamicRange@91a84ee{encoding=SDR, bitDepth=8}, sessionType=0, expectedFrameRateRange=[0, 0], implementationOptions=androidx.camera.camera2.impl.Camera2ImplConfig@315a4a6, zslDisabled=false}, secondaryStreamSpec null
07-17 12:56:19.195 D/DeferrableSurface(27418): Surface created[total_surfaces=1, used_surfaces=0](androidx.camera.core.processing.SurfaceEdge$SettableSurface@6c5dee7}
07-17 12:56:19.195 D/DeferrableSurface(27418): Surface created[total_surfaces=2, used_surfaces=0](androidx.camera.core.SurfaceRequest$2@e878283}
07-17 12:56:19.196 D/DeferrableSurface(27418): New surface in use[total_surfaces=2, used_surfaces=1](androidx.camera.core.SurfaceRequest$2@e878283}
07-17 12:56:19.196 D/DeferrableSurface(27418): use count+1, useCount=1 androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:19.197 D/UseCase (27418): applyFeaturesToConfig: mFeatureGroup = null, this = ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb
07-17 12:56:19.198 D/ImageCapture(27418): onSuggestedStreamSpecUpdated: primaryStreamSpec = StreamSpec{resolution=2320x1740, originalConfiguredResolution=2320x1740, dynamicRange=DynamicRange@91a84ee{encoding=SDR, bitDepth=8}, sessionType=0, expectedFrameRateRange=[0, 0], implementationOptions=androidx.camera.camera2.impl.Camera2ImplConfig@2db92f5, zslDisabled=false}, secondaryStreamSpec null
07-17 12:56:19.198 D/ImageCapture(27418): createPipeline(cameraId: 1, streamSpec: StreamSpec{resolution=2320x1740, originalConfiguredResolution=2320x1740, dynamicRange=DynamicRange@91a84ee{encoding=SDR, bitDepth=8}, sessionType=0, expectedFrameRateRange=[0, 0], implementationOptions=androidx.camera.camera2.impl.Camera2ImplConfig@2db92f5, zslDisabled=false})
07-17 12:56:19.209 D/DeferrableSurface(27418): Surface created[total_surfaces=3, used_surfaces=1](androidx.camera.core.impl.ImmediateSurface@cc2688a}
07-17 12:56:19.211 D/CXCP    (27418): Attaching [Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6, ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>>
07-17 12:56:19.211 D/CXCP    (27418): Prepared UseCaseGraphContext (Deferred)
07-17 12:56:19.212 D/CXCP    (27418): Configured UseCaseCamera-2
07-17 12:56:19.212 D/CXCP    (27418): setFlashAsync: flashMode = 2, requestControl = androidx.camera.camera2.impl.DeferredUseCaseCameraRequestControl@c9f6971
07-17 12:56:19.214 D/CXCP    (27418): Notifying [] camera control ready
07-17 12:56:19.214 D/ImageCapture(27418): onCameraControlReady
07-17 12:56:19.214 D/CXCP    (27418): setFlashAsync: flashMode = 2, requestControl = androidx.camera.camera2.impl.DeferredUseCaseCameraRequestControl@c9f6971
07-17 12:56:19.217 D/CXCP    (27418): populateSurfaceToStreamUseCaseMapping() - streamUseCaseMap = {}
07-17 12:56:19.218 W/CXCP    (27418): Expected stream use case for androidx.camera.core.SurfaceRequest$2@e878283, null cannot be set!
07-17 12:56:19.218 D/ImageCapture(27418): setFlashMode: flashMode = 2
07-17 12:56:19.218 D/CXCP    (27418): setFlashAsync: flashMode = 2, requestControl = androidx.camera.camera2.impl.DeferredUseCaseCameraRequestControl@c9f6971
07-17 12:56:19.219 W/CXCP    (27418): Expected stream use case for androidx.camera.core.impl.ImmediateSurface@cc2688a, null cannot be set!
07-17 12:56:19.219 D/CXCP    (27418): TorchControl#setTorchAsync: torch mode = TorchMode(value=0)
07-17 12:56:19.228 D/CXCP    (27418): Camera 1 has become available
07-17 12:56:19.232 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) (CameraId-1) camera status changed: CameraAvailable(camera=CameraId-1)
07-17 12:56:19.233 D/CXCP    (27418): Camera2CameraController(CameraGraph-2): Not restarting. Controller state = androidx.camera.camera2.pipe.CameraController$ControllerState$STOPPED@c5ceb90, last camera error = null, camera availability = CameraAvailable(camera=CameraId-1), last camera priorities changed = null, current timestamp = TimestampNs(value=1019721838696125).
07-17 12:56:19.234 I/CXCP    (27418): CameraGraph-2 (Camera 1)
07-17 12:56:19.234 I/CXCP    (27418):   Facing:    Front (Physical, Level 3)
07-17 12:56:19.234 I/CXCP    (27418):   Mode:      Normal
07-17 12:56:19.234 I/CXCP    (27418): Outputs:
07-17 12:56:19.234 I/CXCP    (27418):   Stream-3    Output-3    1600x1200   PRIVATE          [DynamicRangeProfile(value=1)] [StreamUseHint(value=0)]
07-17 12:56:19.234 I/CXCP    (27418):   Stream-4    Output-4    2320x1740   JPEG             [DynamicRangeProfile(value=1)] [StreamUseHint(value=0)]
07-17 12:56:19.234 I/CXCP    (27418): Session Template: TEMPLATE_PREVIEW
07-17 12:56:19.234 I/CXCP    (27418): Session Parameters: (None)
07-17 12:56:19.234 I/CXCP    (27418): Default Template: TEMPLATE_PREVIEW
07-17 12:56:19.234 I/CXCP    (27418): Default Parameters
07-17 12:56:19.234 I/CXCP    (27418):   Metadata.Key(androidx.camera.camera2.pipe.captureRequestTag) android.hardware.camera2.CaptureRequest.setTag.CX
07-17 12:56:19.234 I/CXCP    (27418): Required Parameters: (None)
07-17 12:56:19.235 D/CXCP    (27418): Camera graph updated from CameraGraph-1 to CameraGraph-2
07-17 12:56:19.235 I/CXCP    (27418): Starting CameraGraph-2
07-17 12:56:19.235 D/CXCP    (27418): GraphProcessor(cameraGraph: CameraGraph-2) onGraphStarting
07-17 12:56:19.236 D/CXCP    (27418): CameraGraph-2 state updated to GRAPH_STARTING
07-17 12:56:19.236 W/OnBackInvokedCallback(27418): OnBackInvokedCallback is not enabled for the application.
07-17 12:56:19.236 W/OnBackInvokedCallback(27418): Set 'android:enableOnBackInvokedCallback="true"' in the application manifest.
07-17 12:56:19.236 D/CXCP    (27418): Updated current camera internal state to CombinedCameraState(state=OPENING, error=null)
07-17 12:56:19.236 D/PreviewView(27418): Surface requested by Preview.
07-17 12:56:19.238 D/CXCP    (27418): Started Camera2CameraController(CameraGraph-2)
07-17 12:56:19.239 D/CXCP    (27418): Setting up Surfaces with UseCaseSurfaceManager
07-17 12:56:19.239 D/DeferrableSurface(27418): use count+1, useCount=2 androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:19.239 D/DeferrableSurface(27418): New surface in use[total_surfaces=3, used_surfaces=2](androidx.camera.core.impl.ImmediateSurface@cc2688a}
07-17 12:56:19.239 D/DeferrableSurface(27418): use count+1, useCount=1 androidx.camera.core.impl.ImmediateSurface@cc2688a
07-17 12:56:19.239 D/CXCP    (27418): PruningProcessingQueue: Pruning [RequestOpen(virtualCamera=VirtualCamera-2, sharedCameraIds=[], graphListener=GraphProcessor(cameraGraph: CameraGraph-2), isPrewarm=false, isForegroundObserver=androidx.camera.camera2.pipe.compat.Camera2CameraController$$ExternalSyntheticLambda1@79f3845)]
07-17 12:56:19.240 D/CXCP    (27418): Configured androidx.camera.camera2.impl.UseCaseCameraRequestControlImpl@c4e2b9a
07-17 12:56:19.240 D/CXCP    (27418): UseCaseCameraRequestControlImpl#setParametersAsync: [DEFAULT] values = {CaptureRequest.Key(android.control.aeExposureCompensation)=0}, optionPriority = OPTIONAL
07-17 12:56:19.241 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = null, template = RequestTemplate(value=1)
07-17 12:56:19.241 D/CXCP    (27418): UseCaseCameraRequestControlImpl#setParametersAsync: [DEFAULT] values = {CaptureRequest.Key(android.control.zoomRatio)=1.0}, optionPriority = OPTIONAL
07-17 12:56:19.242 D/CXCP    (27418): PruningProcessingQueue: Processing RequestOpen(virtualCamera=VirtualCamera-2, sharedCameraIds=[], graphListener=GraphProcessor(cameraGraph: CameraGraph-2), isPrewarm=false, isForegroundObserver=androidx.camera.camera2.pipe.compat.Camera2CameraController$$ExternalSyntheticLambda1@79f3845)
07-17 12:56:19.242 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0, CaptureRequest.Key(android.control.zoomRatio)=1.0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = null, template = RequestTemplate(value=1)
07-17 12:56:19.242 I/CXCP    (27418): PruningCamera2DeviceManager#processRequestOpen(CameraId-1)
07-17 12:56:19.243 D/CXCP    (27418): UseCaseCameraRequestControlImpl#updateCamera2ConfigAsync
07-17 12:56:19.244 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0, CaptureRequest.Key(android.control.zoomRatio)=1.0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = null, template = RequestTemplate(value=1)
07-17 12:56:19.244 D/CXCP    (27418): UseCaseCameraRequestControlImpl: Building SessionConfig...
07-17 12:56:19.245 D/CXCP    (27418): UseCaseCameraRequestControlImpl: SessionConfig built. Updating state...
07-17 12:56:19.245 D/CXCP    (27418): UseCaseCameraRequestControlImpl: State update processing.
07-17 12:56:19.245 D/CXCP    (27418): Opening CameraId-1 with retries...
07-17 12:56:19.245 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0, CaptureRequest.Key(android.control.zoomRatio)=1.0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = [Stream-3], template = RequestTemplate(value=1)
07-17 12:56:19.245 D/CXCP    (27418): Update RepeatingRequest: Request(streams=[Stream-3], template=RequestTemplate(value=1))@d36766
07-17 12:56:19.247 D/CXCP    (27418): UseCaseCameraRequestControlImpl#updateCamera2ConfigAsync
07-17 12:56:19.247 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0, CaptureRequest.Key(android.control.zoomRatio)=1.0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = null, template = RequestTemplate(value=1)
07-17 12:56:19.247 D/CXCP    (27418): Update RepeatingRequest: Request(streams=[Stream-3], template=RequestTemplate(value=1))@2684aa7
07-17 12:56:19.248 D/CXCP    (27418): State3AControl.getFinalPreferredAeMode: preferAeMode = 1
07-17 12:56:19.248 D/CXCP    (27418): UseCaseCameraRequestControlImpl#setParametersAsync: [DEFAULT] values = {CaptureRequest.Key(android.control.aeMode)=1, CaptureRequest.Key(android.control.afMode)=0, CaptureRequest.Key(android.control.awbMode)=1}, optionPriority = OPTIONAL
07-17 12:56:19.249 D/CXCP    (27418): UseCaseCameraState#updateState: parameters = {CaptureRequest.Key(android.control.aeExposureCompensation)=0, CaptureRequest.Key(android.control.aeMode)=1, CaptureRequest.Key(android.control.afMode)=0, CaptureRequest.Key(android.control.awbMode)=1, CaptureRequest.Key(android.control.zoomRatio)=1.0}, internalParameters = {Metadata.Key(camerax.tag_bundle)=android.hardware.camera2.CaptureRequest.setTag.CX}, streams = null, template = RequestTemplate(value=1)
07-17 12:56:19.249 D/CXCP    (27418): Update RepeatingRequest: Request(streams=[Stream-3], template=RequestTemplate(value=1))@150db54
07-17 12:56:19.249 I/CXCP    (27418): Opening CameraId-1
07-17 12:56:19.249 D/CXCP    (27418): UseCaseCameraState: Updating 3A modes: AE(AeMode(value=1), changed=true), AF(AfMode(value=0), changed=true), AWB(AwbMode(value=1), changed=true)
07-17 12:56:19.249 D/CXCP    (27418): Controller3A#update3A: cancelling previous request null
07-17 12:56:19.260 D/CXCP    (27418): CameraId-1 has become available! Notifying listeners...
07-17 12:56:19.274 D/SurfaceViewImpl(27418): Surface created.
07-17 12:56:19.274 D/SurfaceViewImpl(27418): Surface changed. Size: 1600x1200
07-17 12:56:19.292 D/PreviewView(27418): Preview transformation info updated. TransformationInfo{getCropRect=Rect(0, 0 - 1600, 1200), getRotationDegrees=270, getTargetRotation=-1, hasCameraTransform=true, getSensorToBufferTransform=Matrix{[0.6896551, 0.0, -0.0][0.0, 0.6896551, -0.0][0.0, 0.0, 1.0]}, isMirroring=true}
07-17 12:56:19.292 D/PreviewTransform(27418): Transformation info set: TransformationInfo{getCropRect=Rect(0, 0 - 1600, 1200), getRotationDegrees=270, getTargetRotation=-1, hasCameraTransform=true, getSensorToBufferTransform=Matrix{[0.6896551, 0.0, -0.0][0.0, 0.6896551, -0.0][0.0, 0.0, 1.0]}, isMirroring=true} 1600x1200 true
07-17 12:56:19.294 D/SurfaceViewImpl(27418): Surface set on Preview.
07-17 12:56:19.296 D/CXCP    (27418): Configured Surface(name=null)/@0x7902887 for Stream-3
07-17 12:56:19.296 I/CXCP    (27418): Configured Stream-3 with Surface(name=null)/@0x7902887
07-17 12:56:19.298 D/CXCP    (27418): SurfaceActive androidx.camera.core.SurfaceRequest$2@e878283 in androidx.camera.camera2.impl.UseCaseSurfaceManager@9cbebdd
07-17 12:56:19.298 D/DeferrableSurface(27418): use count+1, useCount=3 androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:19.303 D/CXCP    (27418): Configured Surface(name=null)/@0x42493b4 for Stream-4
07-17 12:56:19.304 I/CXCP    (27418): Configured Stream-4 with Surface(name=null)/@0x42493b4
07-17 12:56:19.305 D/CXCP    (27418): SurfaceActive androidx.camera.core.impl.ImmediateSurface@cc2688a in androidx.camera.camera2.impl.UseCaseSurfaceManager@9cbebdd
07-17 12:56:19.306 D/DeferrableSurface(27418): use count+1, useCount=2 androidx.camera.core.impl.ImmediateSurface@cc2688a
07-17 12:56:19.314 I/CXCP    (27418): Surface setup complete
07-17 12:56:19.315 D/DeferrableSurface(27418): use count-1,  useCount=2 closed=false androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:19.316 D/DeferrableSurface(27418): use count-1,  useCount=1 closed=false androidx.camera.core.impl.ImmediateSurface@cc2688a
07-17 12:56:19.318 I/CXCP    (27418): Unavailable camera 1 detected
07-17 12:56:19.321 D/CXCP    (27418): Camera 1 has become unavailable
07-17 12:56:19.320 I/CXCP    (27418): Loaded CameraIdList [CameraId-0, CameraId-1]
07-17 12:56:19.327 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) (CameraId-1) camera status changed: CameraUnavailable(camera=CameraId-1)
07-17 12:56:19.328 D/CXCP    (27418): Emitting camera ID list: [CameraId-0, CameraId-1]
07-17 12:56:19.330 D/CXCP    (27418): Camera2CameraController(CameraGraph-2): Not restarting. Controller state = androidx.camera.camera2.pipe.CameraController$ControllerState$STARTED@35668f3, last camera error = null, camera availability = CameraUnavailable(camera=CameraId-1), last camera priorities changed = null, current timestamp = TimestampNs(value=1019721934112853).
07-17 12:56:19.437 I/CXCP    (27418): Opened CameraId-1 in 191.621 ms
07-17 12:56:19.437 D/CXCP    (27418): tryOpenCamera: openCamera() for CameraId-1 returned
07-17 12:56:19.438 D/CXCP    (27418): tryOpenCamera: CameraId-1 opened
07-17 12:56:19.438 I/CXCP    (27418): Camera open completed: OpenCameraResult(cameraState=CameraState-2, errorCode=null)
07-17 12:56:19.441 I/CXCP    (27418): PruningCameraDeviceManager: CameraId-1 opened successfully
07-17 12:56:19.442 I/CXCP    (27418): Creating CameraCaptureSession from CameraId-1 using CaptureSessionState-2 with {Stream-3=Surface(name=null)/@0x7902887, Stream-4=Surface(name=null)/@0x42493b4}
07-17 12:56:19.693 D/CXCP    (27418): CXCP#createCaptureSession-1 - 250.831 ms
07-17 12:56:19.694 D/CXCP    (27418): CaptureSessionState-2 Configured
07-17 12:56:19.694 I/CXCP    (27418): Configured CaptureSessionState-2 in 251.998 ms
07-17 12:56:19.694 D/CXCP    (27418): GraphProcessor(cameraGraph: CameraGraph-2) onGraphStarted
07-17 12:56:19.694 D/CXCP    (27418): CameraGraph-2 state updated to GRAPH_STARTED
07-17 12:56:19.694 D/CXCP    (27418): Updated current camera internal state to CombinedCameraState(state=OPEN, error=null)
07-17 12:56:19.694 D/CXCP    (27418): CaptureSessionState-2 Ready
07-17 12:56:19.695 D/CXCP    (27418): Building CaptureRequest for Request(streams=[Stream-3], template=RequestTemplate(value=1))@150db54
07-17 12:56:19.695 D/CXCP    (27418): CXCP#createCaptureRequest-1 - 0.766 ms
07-17 12:56:19.697 D/CXCP    (27418): GraphRequestProcessor-2 submitting Camera2CaptureSequence-3
07-17 12:56:19.702 D/CXCP    (27418): CXCP#setRepeatingRequest-1 - 3.470 ms
07-17 12:56:19.702 D/CXCP    (27418): CaptureSessionState-2 Active
07-17 12:56:19.702 D/CXCP    (27418): GraphRequestProcessor-2 submitted Camera2CaptureSequence-3
07-17 12:56:19.823 D/StreamStateObserver(27418): Update Preview stream state to STREAMING
07-17 12:56:19.955 W/chromium(27418): [WARNING:net/spdy/spdy_session.cc:3186] Received HEADERS for invalid stream 81
07-17 12:56:22.458 D/CXCP    (27418): Camera access priorities have changed
07-17 12:56:22.461 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) (CameraId-1) camera status changed: CameraPrioritiesChanged
07-17 12:56:22.462 D/CXCP    (27418): Camera2CameraController(CameraGraph-2): Not restarting. Controller state = androidx.camera.camera2.pipe.CameraController$ControllerState$STARTED@35668f3, last camera error = null, camera availability = CameraUnavailable(camera=CameraId-1), last camera priorities changed = TimestampNs(value=1019725068065978), current timestamp = TimestampNs(value=1019725068091291).
07-17 12:56:22.796 D/ImageCapture(27418): takePictureInternal
07-17 12:56:22.797 D/TakePictureManagerImpl(27418): Issue the next TakePictureRequest.
07-17 12:56:22.797 D/ProcessingRequest(27418): ProcessingRequest: mRequestId = 1, mTagBundleKey = 146197987
07-17 12:56:22.801 D/CXCP    (27418): StillCaptureRequestControl: submitting CaptureRequest(captureConfigs=[androidx.camera.core.impl.CaptureConfig@74d6d5e], captureMode=0, flashType=0, result=CompletableDeferredImpl{Active}@7fe653f) at androidx.camera.camera2.impl.DeferredUseCaseCameraRequestControl@c9f6971
07-17 12:56:22.803 D/CXCP    (27418): FlashControl: Waiting for any ongoing update to be completed
07-17 12:56:22.803 D/CXCP    (27418): awaitFlashModeUpdate: initialFlashMode = 2
07-17 12:56:22.805 D/CXCP    (27418): StillCaptureRequestControl: Issuing single capture
07-17 12:56:22.811 D/CXCP    (27418): UseCaseCameraRequestControlImpl#issueSingleCaptureAsync
07-17 12:56:22.820 D/CXCP    (27418): UseCaseCameraRequestControl: Submitting still captures to capture pipeline
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#invokeCaptureTasks: tasks = [PRE_CAPTURE, MAIN_CAPTURE, POST_CAPTURE], captureMode = 0, flashMode = 2, flashType = 0
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#defaultNoFlashCapture
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: tasks = [PRE_CAPTURE, MAIN_CAPTURE, POST_CAPTURE]
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: starting PRE_CAPTURE
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#defaultNoFlashCapture: Locking 3A
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#defaultNoFlashCapture: Locking 3A done
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: PRE_CAPTURE completed
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: starting MAIN_CAPTURE
07-17 12:56:22.822 D/CXCP    (27418): CapturePipeline#submitRequestInternal; Submitting [androidx.camera.core.impl.CaptureConfig@74d6d5e] with CameraPipe
07-17 12:56:22.824 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: MAIN_CAPTURE completed
07-17 12:56:22.825 D/CXCP    (27418): CapturePipeline#submitRequestInternal: Acquiring session for submitting requests
07-17 12:56:22.825 D/CXCP    (27418): CapturePipeline#submitRequestInternal: Submitting [Request(streams=[Stream-4], template=RequestTemplate(value=2))@9b12ce6]
07-17 12:56:22.825 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: Waiting for POST_CAPTURE signal
07-17 12:56:22.826 D/CXCP    (27418): StillCaptureRequestControl: Waiting for deferred list from CaptureRequest(captureConfigs=[androidx.camera.core.impl.CaptureConfig@74d6d5e], captureMode=0, flashType=0, result=CompletableDeferredImpl{Active}@7fe653f)
07-17 12:56:22.828 D/CXCP    (27418): Building CaptureRequest for Request(streams=[Stream-4], template=RequestTemplate(value=2))@9b12ce6
07-17 12:56:22.830 D/CXCP    (27418): CXCP#createCaptureRequest-1 - 1.093 ms
07-17 12:56:22.831 D/CXCP    (27418): GraphRequestProcessor-2 submitting Camera2CaptureSequence-4
07-17 12:56:22.837 D/CXCP    (27418): CXCP#capture-1 - 1.814 ms
07-17 12:56:22.838 D/CXCP    (27418): GraphRequestProcessor-2 submitted Camera2CaptureSequence-4
07-17 12:56:22.845 D/CXCP    (27418): CaptureSessionState-2 CaptureQueueEmpty
07-17 12:56:23.079 D/ProcessingRequest(27418): onCaptureStarted: request ID = 1
07-17 12:56:23.239 D/CXCP    (27418): Camera access priorities have changed
07-17 12:56:23.240 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) (CameraId-1) camera status changed: CameraPrioritiesChanged
07-17 12:56:23.240 D/CXCP    (27418): Camera2CameraController(CameraGraph-2): Not restarting. Controller state = androidx.camera.camera2.pipe.CameraController$ControllerState$STARTED@35668f3, last camera error = null, camera availability = CameraUnavailable(camera=CameraId-1), last camera priorities changed = TimestampNs(value=1019725846516420), current timestamp = TimestampNs(value=1019725846531472).
07-17 12:56:23.436 W/chromium(27418): [WARNING:net/spdy/spdy_session.cc:3186] Received HEADERS for invalid stream 83
07-17 12:56:23.598 D/CXCP    (27418): StillCaptureRequestControl: Waiting for deferred list from CaptureRequest(captureConfigs=[androidx.camera.core.impl.CaptureConfig@74d6d5e], captureMode=0, flashType=0, result=CompletableDeferredImpl{Active}@7fe653f) done
07-17 12:56:23.599 D/CXCP    (27418): CapturePipeline#List<PipelineTask>.invoke: Waiting for POST_CAPTURE signal done
07-17 12:56:23.599 D/CXCP    (27418): CapturePipeline#defaultNoFlashCapture: Unlocking 3A
07-17 12:56:23.599 D/CXCP    (27418): CapturePipeline#defaultNoFlashCapture: Unlocking 3A done
07-17 12:56:23.628 D/qdgralloc(27418): GetYUVPlaneInfo: Invalid format passed: 0x21
07-17 12:56:23.629 D/CaptureNode(27418): OnImageAvailableListener: mCurrentRequest ID = 1, image.isNull = false
07-17 12:56:23.630 I/ProcessingRequest(27418): onImageCaptured: request ID = 1
07-17 12:56:23.631 D/TakePictureManagerImpl(27418): Issue the next TakePictureRequest.
07-17 12:56:23.631 D/ProcessingNode(27418): processInMemoryCapture: request ID = 1
07-17 12:56:23.632 D/TakePictureManagerImpl(27418): No new request.
07-17 12:56:23.642 I/ProcessingRequest(27418): onFinalResult(ImageProxy): request ID = 1
07-17 12:56:24.020 D/DeferrableSurface(27418): surface closed,  useCount=2 closed=true androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:24.020 D/DeferrableSurface(27418): surface closed,  useCount=0 closed=true androidx.camera.core.processing.SurfaceEdge$SettableSurface@6c5dee7
07-17 12:56:24.020 D/DeferrableSurface(27418): Surface terminated[total_surfaces=2, used_surfaces=2](androidx.camera.core.processing.SurfaceEdge$SettableSurface@6c5dee7}
07-17 12:56:24.020 D/DeferrableSurface(27418): use count-1,  useCount=1 closed=true androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:24.020 W/ScreenFlashWrapper(27418): completePendingScreenFlashClear: none pending!
07-17 12:56:24.020 D/ImageCapture(27418): clearPipeline
07-17 12:56:24.021 D/DeferrableSurface(27418): surface closed,  useCount=1 closed=true androidx.camera.core.impl.ImmediateSurface@cc2688a
07-17 12:56:24.021 D/CXCP    (27418): Detaching [Preview:androidx.camera.core.Preview-5dfc42fb-0df7-485e-bef2-c40394bf97b6, ImageCapture:androidx.camera.core.ImageCapture-5973d2bd-3607-4565-b872-7b1269dc1feb] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>>
07-17 12:56:24.021 W/ScreenFlashWrapper(27418): completePendingScreenFlashClear: none pending!
07-17 12:56:24.022 D/CXCP    (27418): UseCaseCameraRequestControl: closed
07-17 12:56:24.022 D/CXCP    (27418): LowLightBoostControl#setLowLightBoostAsync: lowLightBoost = false
07-17 12:56:24.022 D/CXCP    (27418): setFlashAsync: flashMode = 2, requestControl = null
07-17 12:56:24.023 D/CXCP    (27418): setFlashAsync: flashMode = 2, requestControl = null
07-17 12:56:24.024 D/CXCP    (27418): reset: videoUsage = 0
07-17 12:56:24.024 W/CXCP    (27418): Attach [] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>> (Ignored)
07-17 12:56:24.024 W/CXCP    (27418): Detaching [] from UseCaseManager<CameraGraphConfigProvider<CameraId-1>> (Ignored)
07-17 12:56:24.024 D/CXCP    (27418): Closing UseCaseCamera-2
07-17 12:56:24.025 I/CXCP    (27418): Closing CameraGraph-2
07-17 12:56:24.027 D/CXCP    (27418): Closing GraphRequestProcessor-2
07-17 12:56:24.027 D/SurfaceViewImpl(27418): Surface destroyed.
07-17 12:56:24.027 D/SurfaceViewImpl(27418): Surface closed androidx.camera.core.SurfaceRequest@7676532
07-17 12:56:24.027 D/Surface (27418): Surface::disconnect
07-17 12:56:24.027 D/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) disconnect: api -1
07-17 12:56:24.029 D/CXCP    (27418): Closed Camera2CameraController(CameraGraph-2)
07-17 12:56:24.029 D/CXCP    (27418): Closing GraphRequestProcessor-2
07-17 12:56:24.031 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) queueBuffer: BufferQueue has been abandoned
07-17 12:56:24.043 D/CXCP    (27418): GraphProcessor(cameraGraph: CameraGraph-2) onGraphStopping
07-17 12:56:24.043 D/CXCP    (27418): CameraGraph-2 state updated to GRAPH_STOPPING
07-17 12:56:24.043 D/CXCP    (27418): Updated current camera internal state to CombinedCameraState(state=CLOSING, error=null)
07-17 12:56:24.045 D/CXCP    (27418): CaptureSessionState-2 Shutdown
07-17 12:56:24.047 D/CXCP    (27418): Camera2CaptureSequenceProcessor-2#stopRepeating
07-17 12:56:24.052 D/CXCP    (27418): CXCP#stopRepeating-1 - 0.680 ms
07-17 12:56:24.053 D/CXCP    (27418): Camera2CaptureSequenceProcessor-2#abortCaptures
07-17 12:56:24.054 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) dequeueBuffer: BufferQueue has been abandoned
07-17 12:56:24.073 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) queueBuffer: BufferQueue has been abandoned
07-17 12:56:24.084 D/StreamStateObserver(27418): Update Preview stream state to IDLE
07-17 12:56:24.087 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) cancelBuffer: BufferQueue has been abandoned
07-17 12:56:24.087 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) cancelBuffer: BufferQueue has been abandoned
07-17 12:56:24.088 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) cancelBuffer: BufferQueue has been abandoned
07-17 12:56:24.089 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) cancelBuffer: BufferQueue has been abandoned
07-17 12:56:24.089 E/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) cancelBuffer: BufferQueue has been abandoned
07-17 12:56:24.092 D/CXCP    (27418): CXCP#abortCaptures-1 - 36.749 ms
07-17 12:56:24.094 D/CXCP    (27418): Closing capture session for CaptureSessionState-2
07-17 12:56:24.095 D/CXCP    (27418): GraphProcessor(cameraGraph: CameraGraph-2) onGraphStopped
07-17 12:56:24.095 D/CXCP    (27418): CameraGraph-2 state updated to GRAPH_STOPPED
07-17 12:56:24.095 D/CXCP    (27418): Updated current camera internal state to CombinedCameraState(state=CLOSED, error=null)
07-17 12:56:24.096 I/CXCP    (27418): Disconnecting VirtualCamera-2
07-17 12:56:24.097 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) is closed
07-17 12:56:24.097 D/CXCP    (27418): Camera2CameraController(CameraGraph-2) finalized
07-17 12:56:24.097 D/CXCP    (27418): CaptureSessionState-2 Ready
07-17 12:56:24.098 D/CXCP    (27418): CaptureSessionState-2 Closed
07-17 12:56:24.101 D/CXCP    (27418): CaptureSessionState-2 session finalizing
07-17 12:56:24.101 D/CXCP    (27418): Finalizing CaptureSessionState-2
07-17 12:56:24.102 D/CXCP    (27418): SurfaceInactive androidx.camera.core.SurfaceRequest$2@e878283 in androidx.camera.camera2.impl.UseCaseSurfaceManager@9cbebdd
07-17 12:56:24.102 D/DeferrableSurface(27418): use count-1,  useCount=0 closed=true androidx.camera.core.SurfaceRequest$2@e878283
07-17 12:56:24.102 D/DeferrableSurface(27418): Surface no longer in use[total_surfaces=2, used_surfaces=1](androidx.camera.core.SurfaceRequest$2@e878283}
07-17 12:56:24.102 D/DeferrableSurface(27418): Surface terminated[total_surfaces=1, used_surfaces=1](androidx.camera.core.SurfaceRequest$2@e878283}
07-17 12:56:24.102 D/CXCP    (27418): SurfaceInactive androidx.camera.core.impl.ImmediateSurface@cc2688a in androidx.camera.camera2.impl.UseCaseSurfaceManager@9cbebdd
07-17 12:56:24.102 D/DeferrableSurface(27418): use count-1,  useCount=0 closed=true androidx.camera.core.impl.ImmediateSurface@cc2688a
07-17 12:56:24.102 D/DeferrableSurface(27418): Surface no longer in use[total_surfaces=1, used_surfaces=0](androidx.camera.core.impl.ImmediateSurface@cc2688a}
07-17 12:56:24.102 D/DeferrableSurface(27418): Surface terminated[total_surfaces=0, used_surfaces=0](androidx.camera.core.impl.ImmediateSurface@cc2688a}
07-17 12:56:24.102 D/CXCP    (27418): androidx.camera.camera2.impl.UseCaseSurfaceManager@9cbebdd remove surface listener
07-17 12:56:24.103 D/BufferQueueProducer(27418): [SurfaceView[com.ejemplos.devices.integrada.hibrida/crc643a178092b76b4b46.MainActivity]#2(BLAST Consumer)2](id:6b1a00000004,api:4,p:1577,c:27418) disconnect: api 4
07-17 12:56:24.104 D/BufferQueueProducer(27418): [ImageReader-2320x1740f21m4-27418-1](id:6b1a00000003,api:4,p:1577,c:27418) disconnect: api 4
07-17 12:56:24.105 W/CameraDevice-JV-1(27418): Stream configuration failed due to: endConfigure:665: Camera 1: Unsupported set of inputs/outputs provided
07-17 12:56:24.118 D/SurfaceViewImpl(27418): Safe to release surface.
El subproceso 37 terminó con código 0 (0x0).
07-17 12:56:24.767 W/chromium(27418): [WARNING:net/spdy/spdy_session.cc:3186] Received HEADERS for invalid stream 89
07-17 12:56:25.100 D/CXCP    (27418): PruningProcessingQueue: Pruning [RequestClose(activeCamera=ActiveCamera(cameraId=CameraId-1)@ec2d69b)]
07-17 12:56:25.102 D/CXCP    (27418): PruningProcessingQueue: Processing RequestClose(activeCamera=ActiveCamera(cameraId=CameraId-1)@ec2d69b)
07-17 12:56:25.102 I/CXCP    (27418): PruningCamera2DeviceManager#processRequestClose(CameraId-1)
07-17 12:56:25.103 D/CXCP    (27418): handleQuirksBeforeClosing(android.hardware.camera2.impl.CameraDeviceImpl@c7ea567)
07-17 12:56:25.103 D/CXCP    (27418): CaptureSessionState-2 session disconnecting
07-17 12:56:25.103 D/CXCP    (27418): closeCameraDevice(1)
07-17 12:56:25.105 I/CXCP    (27418): Closing Camera 1
07-17 12:56:25.271 D/CXCP    (27418): Emitting camera ID list: [CameraId-0, CameraId-1]
07-17 12:56:25.273 D/CXCP    (27418): CameraId-1: onClosed
07-17 12:56:25.273 D/CXCP    (27418): CXCP#CameraDevice-1#close - 167.816 ms
07-17 12:56:25.274 D/CXCP    (27418): CameraState-2: onFinalized
07-17 12:56:26.877 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:26.866Z] Warning: Error from HTTP request. TypeError: Failed to fetch.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
07-17 12:56:26.879 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:26.876Z] Warning: Error from HTTP request. TypeError: Failed to fetch.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
07-17 12:56:26.880 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:26.877Z] Error: Connection disconnected with error 'TypeError: Failed to fetch'.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
07-17 12:56:26.884 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:26.878Z] Warning: Failed to deliver completion notification for render '13'.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
07-17 12:56:27.012 W/chromium(27418): [WARNING:net/spdy/spdy_session.cc:3186] Received HEADERS for invalid stream 99
07-17 12:56:27.262 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:27.261Z] Information: Normalizing '_blazor' to 'https://aplicada.somee.com/_blazor'.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
07-17 12:56:28.075 I/chromium(27418): [INFO:CONSOLE:1] "[2026-07-17T15:56:28.073Z] Warning: Failed to connect via WebSockets, using the Long Polling fallback transport. This may be due to a VPN or proxy blocking the connection. To troubleshoot this, visit https://aka.ms/blazor-server-using-fallback-long-polling.", source: https://aplicada.somee.com/_framework/blazor.web.ne14ti1q68.js (1)
El subproceso '.NET TP Worker' (55) terminó con código 0 (0x0).
El subproceso '.NET TP Worker' (52) terminó con código 0 (0x0).
El subproceso '.NET TP Worker' (50) terminó con código 0 (0x0).
El subproceso '.NET TP Worker' (51) terminó con código 0 (0x0).
