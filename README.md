```python


def build_single_chatbot(chatbot_id):
    chatbot_id = str(chatbot_id)
    _id = gr.Textbox(visible=False, value=chatbot_id)
    gr.Markdown(f"# <h3>🕵️‍♂️{label_model[int(chatbot_id)]}</h3>")
    show_btn = gr.Button("Show Modal")

    with Modal(visible=False, allow_user_close=True) as modal:
        gr.Markdown("# 管理模型")
        with gr.Group(elem_id="share-region-named" + chatbot_id):
            with gr.Row(elem_id="model_selector_row" + chatbot_id):
                model_selector = build_model_selector(chatbot_id)
                button_fresh = gr.Button("fresh", scale=1, interactive=True, visible=True)
            with gr.Row(elem_id="model_selector_row"):    
                output_state = gr.Textbox(placeholder="请先选择模型", show_label=False, container=True, interactive=False, max_lines=3)
                model_state = gr.State("ready")

        # with gr.Accordion(open=True, visible=True):
        with gr.Row(elem_id="model_selector_row" + chatbot_id):
            load_btn= gr.Button("加载模型", scale=3, interactive=False, size="sm")
            unload_btn= gr.Button("卸载模型", scale=3, interactive=False, size="sm")
            unload_btn_= gr.Button("刷新", scale=3, interactive=False, size="sm")
            chat_id = gr.Text(value=chatbot_id, visible=False)

            button_fresh.click(
                build_model_selector,
                gr.Text(value=chatbot_id, visible=False),
                model_selector,
            )
            output_state.change(
                listen_output_state,
                [model_selector, model_state, output_state],
                [output_state, model_state, unload_btn, load_btn],
                show_progress="hidden",
            )

            model_selector.change(listen_model_status, [model_selector], [output_state,model_state])

            load_btn.click(
                load_model_btn_operate,
                inputs=[model_selector],
                outputs=[model_state, output_state, model_selector, load_btn, unload_btn],
                show_progress="hidden",
            )
            unload_btn.click(
                unload_model_btn_operate,
                inputs=[model_selector],
                outputs=[model_state, output_state, model_selector, unload_btn, load_btn],
                show_progress="hidden",
            )        
        with gr.Accordion("对话历史", open=True, visible=False) as chatbot_row:
            chatbot = gr.Chatbot(
                elem_id="chatbot" + chatbot_id,
                elem_classes=["chatbot" + chatbot_id],
                label="Model " + chatbot_id,
                height=300,
            )
        show_btn.click(lambda: Modal(visible=True), None, modal)

    return model_selector, chatbot




```
