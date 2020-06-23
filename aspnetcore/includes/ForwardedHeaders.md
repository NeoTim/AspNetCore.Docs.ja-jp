<span data-ttu-id="4621a-101">Forwarded Headers Middleware は、他のミドルウェアの前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4621a-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="4621a-102">この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4621a-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="4621a-103">診断およびエラー処理ミドルウェアの後に Forwarded Headers Middleware を実行する方法については、「[Forwarded Headers Middleware の順序](xref:host-and-deploy/proxy-load-balancer#fhmo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4621a-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>